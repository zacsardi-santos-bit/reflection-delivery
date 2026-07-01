FROM golang:1.21

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

RUN mkdir -p /tmp/dockermock

RUN cat > /tmp/dockermock/go.mod << 'MODEOF'
module dockermock

go 1.21
MODEOF

RUN cat > /tmp/dockermock/main.go << 'GOEOF'
package main

import (
	"archive/tar"
	"bytes"
	"crypto/ecdsa"
	"crypto/elliptic"
	"crypto/rand"
	"crypto/tls"
	"crypto/x509"
	"crypto/x509/pkix"
	"encoding/base64"
	"encoding/json"
	"encoding/pem"
	"fmt"
	"log"
	"math/big"
	"net"
	"net/http"
	"os"
	"strings"
	"time"
)

var caCertPEM []byte
var tlsCert tls.Certificate

const (
	fakeContainerID = "aaabbbccc1234567890aaabbbccc1234567890ab"
	esPort          = 19200
)

func main() {
	if err := genCerts(); err != nil {
		log.Fatalf("genCerts: %v", err)
	}
	go runFakeES()
	time.Sleep(300 * time.Millisecond)
	os.Remove("/var/run/docker.sock")
	ln, err := net.Listen("unix", "/var/run/docker.sock")
	if err != nil {
		log.Fatalf("unix listen: %v", err)
	}
	os.Chmod("/var/run/docker.sock", 0666)
	log.Printf("dockermock: up, fake ES on :%d", esPort)
	http.Serve(ln, http.HandlerFunc(dockerHandler))
}

func genCerts() error {
	caKey, err := ecdsa.GenerateKey(elliptic.P256(), rand.Reader)
	if err != nil {
		return err
	}
	caTemplate := &x509.Certificate{
		SerialNumber: big.NewInt(1), Subject: pkix.Name{CommonName: "Mock CA"},
		NotBefore: time.Now().Add(-time.Hour), NotAfter: time.Now().Add(365 * 24 * time.Hour),
		IsCA: true, BasicConstraintsValid: true,
		KeyUsage: x509.KeyUsageCertSign | x509.KeyUsageCRLSign,
	}
	caDER, err := x509.CreateCertificate(rand.Reader, caTemplate, caTemplate, &caKey.PublicKey, caKey)
	if err != nil {
		return err
	}
	caCert, _ := x509.ParseCertificate(caDER)
	caCertPEM = pem.EncodeToMemory(&pem.Block{Type: "CERTIFICATE", Bytes: caDER})
	esKey, err := ecdsa.GenerateKey(elliptic.P256(), rand.Reader)
	if err != nil {
		return err
	}
	esTemplate := &x509.Certificate{
		SerialNumber: big.NewInt(2), Subject: pkix.Name{CommonName: "localhost"},
		DNSNames: []string{"localhost"}, IPAddresses: []net.IP{net.ParseIP("127.0.0.1")},
		NotBefore: time.Now().Add(-time.Hour), NotAfter: time.Now().Add(365 * 24 * time.Hour),
	}
	esDER, err := x509.CreateCertificate(rand.Reader, esTemplate, caCert, &esKey.PublicKey, caKey)
	if err != nil {
		return err
	}
	esCertPEM := pem.EncodeToMemory(&pem.Block{Type: "CERTIFICATE", Bytes: esDER})
	esKeyDER, err := x509.MarshalECPrivateKey(esKey)
	if err != nil {
		return err
	}
	esKeyPEM := pem.EncodeToMemory(&pem.Block{Type: "EC PRIVATE KEY", Bytes: esKeyDER})
	tlsCert, err = tls.X509KeyPair(esCertPEM, esKeyPEM)
	return err
}

func runFakeES() {
	mux := http.NewServeMux()
	mux.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
		log.Printf("fakeES: %s %s", r.Method, r.URL.Path)
		w.Header().Set("Content-Type", "application/json")
		if strings.Contains(r.URL.Path, "_cluster/health") {
			json.NewEncoder(w).Encode(map[string]interface{}{
				"status": "green", "cluster_name": "mock", "timed_out": false,
				"number_of_nodes": 1, "number_of_data_nodes": 1,
			})
			return
		}
		json.NewEncoder(w).Encode(map[string]interface{}{
			"name": "mock-node", "cluster_name": "mock",
			"version": map[string]interface{}{"number": "8.13.0"},
			"tagline": "You Know, for Search",
		})
	})
	ln, err := tls.Listen("tcp", fmt.Sprintf(":%d", esPort), &tls.Config{
		Certificates: []tls.Certificate{tlsCert}, MinVersion: tls.VersionTLS12,
	})
	if err != nil {
		log.Fatalf("ES listen: %v", err)
	}
	log.Printf("fakeES: listening on :%d", esPort)
	http.Serve(ln, mux)
}

func normPath(raw string) string {
	p := raw
	if len(p) > 2 && p[0] == '/' && p[1] == 'v' {
		for i := 2; i < len(p); i++ {
			if p[i] == '/' {
				return p[i:]
			}
		}
	}
	return p
}

func dockerHandler(w http.ResponseWriter, r *http.Request) {
	path := normPath(r.URL.Path)
	log.Printf("docker: %s %s", r.Method, path)
	w.Header().Set("Content-Type", "application/json")
	switch {
	case strings.HasSuffix(path, "/_ping"):
		w.Header().Set("Api-Version", "1.44")
		w.Header().Set("Docker-Experimental", "false")
		w.Header().Set("Ostype", "linux")
		w.Header().Set("Content-Type", "text/plain; charset=utf-8")
		fmt.Fprint(w, "OK")
	case path == "/version":
		json.NewEncoder(w).Encode(map[string]interface{}{
			"Version": "24.0.0", "ApiVersion": "1.44", "MinAPIVersion": "1.12",
			"Os": "linux", "Arch": "amd64", "KernelVersion": "5.15.0",
		})
	case path == "/info":
		json.NewEncoder(w).Encode(map[string]interface{}{
			"ID": "mock", "OSType": "linux", "Architecture": "x86_64",
			"NCPU": 4, "MemTotal": 8589934592, "DockerRootDir": "/var/lib/docker",
		})
	case strings.Contains(path, "/images/create"):
		json.NewEncoder(w).Encode(map[string]string{"status": "Pull complete"})
	case r.Method == "GET" && strings.Contains(path, "/images/") && strings.HasSuffix(path, "/json"):
		json.NewEncoder(w).Encode(map[string]interface{}{
			"Id": "sha256:fakeid", "RepoTags": []string{"fake:latest"}, "Size": 1000000,
		})
	case r.Method == "POST" && strings.HasSuffix(path, "/containers/create"):
		w.WriteHeader(201)
		json.NewEncoder(w).Encode(map[string]interface{}{"Id": fakeContainerID, "Warnings": []string{}})
	case strings.Contains(path, "/containers/"+fakeContainerID):
		sub := path[strings.Index(path, "/containers/"+fakeContainerID)+len("/containers/"+fakeContainerID):]
		handleContainer(w, r, sub)
	case r.Method == "GET" && strings.HasSuffix(path, "/containers/json"):
		json.NewEncoder(w).Encode([]interface{}{})
	case path == "/events":
		time.Sleep(200 * time.Millisecond)
	case strings.Contains(path, "/networks"):
		if r.Method == "POST" && strings.HasSuffix(path, "/networks/create") {
			w.WriteHeader(201)
			json.NewEncoder(w).Encode(map[string]string{"Id": "netid", "Warning": ""})
		} else if r.Method == "DELETE" {
			w.WriteHeader(204)
		} else if r.Method == "POST" {
			w.WriteHeader(200)
		} else {
			json.NewEncoder(w).Encode([]interface{}{})
		}
	case r.Method == "POST" && strings.Contains(path, "/exec") && strings.HasSuffix(path, "/exec"):
		w.WriteHeader(201)
		json.NewEncoder(w).Encode(map[string]string{"Id": "execid"})
	case strings.Contains(path, "/exec/execid"):
		if r.Method == "POST" && strings.HasSuffix(path, "/start") {
			data := caCertPEM
			sz := uint32(len(data))
			w.Write([]byte{1, 0, 0, 0, byte(sz >> 24), byte(sz >> 16), byte(sz >> 8), byte(sz)})
			w.Write(data)
		} else if r.Method == "GET" {
			json.NewEncoder(w).Encode(map[string]interface{}{"ID": "execid", "Running": false, "ExitCode": 0})
		}
	default:
		log.Printf("docker: UNHANDLED %s %s", r.Method, r.URL.Path)
		json.NewEncoder(w).Encode(map[string]interface{}{})
	}
}

func handleContainer(w http.ResponseWriter, r *http.Request, sub string) {
	w.Header().Set("Content-Type", "application/json")
	switch {
	case sub == "/start" && r.Method == "POST":
		w.WriteHeader(204)
	case sub == "/stop" && r.Method == "POST":
		w.WriteHeader(204)
	case sub == "/kill" && r.Method == "POST":
		w.WriteHeader(204)
	case sub == "" || sub == "/json":
		json.NewEncoder(w).Encode(containerInspect())
	case strings.HasPrefix(sub, "/archive"):
		tb := makeCertTar()
		stat, _ := json.Marshal(map[string]interface{}{
			"name": "http_ca.crt", "size": len(caCertPEM), "mode": 420,
			"mtime": "2024-01-01T00:00:00Z", "linkTarget": "",
		})
		w.Header().Set("Content-Type", "application/x-tar")
		w.Header().Set("X-Docker-Container-Path-Stat", base64.StdEncoding.EncodeToString(stat))
		w.Write(tb)
	case sub == "/logs" || strings.HasPrefix(sub, "/logs"):
		w.Header().Set("Content-Type", "application/vnd.docker.raw-stream")
		msg := []byte("{\"message\":\"started\",\"node.name\":\"mock\"}\n")
		w.Write([]byte{1, 0, 0, 0, 0, 0, 0, byte(len(msg))})
		w.Write(msg)
	case r.Method == "DELETE":
		w.WriteHeader(204)
	case r.Method == "POST" && strings.HasSuffix(sub, "/exec"):
		w.WriteHeader(201)
		json.NewEncoder(w).Encode(map[string]string{"Id": "execid"})
	default:
		log.Printf("container: UNHANDLED sub=%s method=%s", sub, r.Method)
		json.NewEncoder(w).Encode(map[string]interface{}{})
	}
}

func containerInspect() interface{} {
	return map[string]interface{}{
		"Id": fakeContainerID,
		"State": map[string]interface{}{
			"Status": "running", "Running": true, "ExitCode": 0,
			"StartedAt": "2024-01-01T00:00:00Z",
		},
		"Config": map[string]interface{}{
			"Image": "docker.elastic.co/elasticsearch/elasticsearch:8.13.0",
			"ExposedPorts": map[string]interface{}{
				"9200/tcp": map[string]interface{}{},
				"9300/tcp": map[string]interface{}{},
			},
			"Env": []string{}, "Labels": map[string]interface{}{},
		},
		"NetworkSettings": map[string]interface{}{
			"IPAddress": "127.0.0.1",
			"Ports": map[string]interface{}{
				"9200/tcp": []map[string]string{{"HostIp": "0.0.0.0", "HostPort": fmt.Sprintf("%d", esPort)}},
				"9300/tcp": []map[string]string{{"HostIp": "0.0.0.0", "HostPort": "19300"}},
			},
			"Networks": map[string]interface{}{
				"bridge": map[string]interface{}{"IPAddress": "127.0.0.1", "Gateway": "172.17.0.1"},
			},
		},
		"HostConfig": map[string]interface{}{
			"PortBindings": map[string]interface{}{
				"9200/tcp": []map[string]string{{"HostIp": "", "HostPort": fmt.Sprintf("%d", esPort)}},
			},
		},
		"Mounts": []interface{}{},
	}
}

func makeCertTar() []byte {
	var buf bytes.Buffer
	tw := tar.NewWriter(&buf)
	tw.WriteHeader(&tar.Header{
		Name: "http_ca.crt", Mode: 0644, Size: int64(len(caCertPEM)), Typeflag: tar.TypeReg,
	})
	tw.Write(caCertPEM)
	tw.Close()
	return buf.Bytes()
}
GOEOF

RUN cd /tmp/dockermock && go build -o /usr/local/bin/dockermock . && rm -rf /tmp/dockermock

RUN mkdir -p /tmp/tcpredl && \
    cd /tmp/tcpredl && \
    go mod init predl && \
    GONOSUMDB=* go get -d github.com/testcontainers/testcontainers-go@v0.31.0 2>/dev/null || true && \
    GONOSUMDB=* go get -d github.com/testcontainers/testcontainers-go/modules/elasticsearch@v0.31.0 2>/dev/null || true && \
    cd / && rm -rf /tmp/tcpredl

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/elastic/go-elasticsearch.git && \
    git fetch --depth 30 origin 3473b3a0ef271235a5929c0b54d5dcbc75dcc796 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN go mod download

RUN go build ./... || true

WORKDIR /workspace

# --- terminal-bench eval harness deps (tmux + asciinema), baked for offline eval ---
RUN (command -v tmux >/dev/null 2>&1 && command -v asciinema >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux asciinema) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux python3-pip && pip3 install --no-cache-dir asciinema) \
    || (apk add --no-cache tmux asciinema) \
    || (dnf install -y tmux asciinema) \
    || (yum install -y tmux asciinema) \
    || true

# --- offline-harbor: ensure grading(python3) + patch(patch/git) deps (build-time) ---
RUN (command -v python3 >/dev/null 2>&1 && command -v patch >/dev/null 2>&1 && command -v git >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends python3 patch git ca-certificates) \
    || (apk add --no-cache python3 patch git ca-certificates) \
    || (dnf install -y python3 patch git ca-certificates) \
    || (yum install -y python3 patch git ca-certificates) \
    || true

# --- offline-harbor go-bake v1 ---
RUN set +e; cd /workspace/repo && for gm in $(find . -name go.mod -not -path '*/vendor/*' -not -path '*/testdata/*' -not -path '*/node_modules/*' 2>/dev/null); do d=$(dirname "$gm"); ( cd "$d" && GOWORK=off GOFLAGS=-mod=mod go mod download all 2>/dev/null ); done; true
RUN set +e; cd /workspace/repo/internal/testing 2>/dev/null || cd /workspace/repo; GOWORK=off GOFLAGS=-mod=mod go test -count=1 -run '^$' ./e2e 2>/dev/null; true
RUN cat > /tmp/_gobake_coords.txt <<'__GOBAKE__'
dario.cat/mergo@v1.0.0
github.com/AdaLogics/go-fuzz-headers@v0.0.0-20230811130428-ced1acdcaa24
github.com/Azure/go-ansiterm@v0.0.0-20210617225240-d185dfc1b5a1
github.com/Microsoft/go-winio@v0.6.1
github.com/Microsoft/hcsshim@v0.11.4
github.com/cenkalti/backoff/v4@v4.2.1
github.com/containerd/containerd@v1.7.15
github.com/containerd/log@v0.1.0
github.com/cpuguy83/dockercfg@v0.3.1
github.com/creack/pty@v1.1.18
github.com/davecgh/go-spew@v1.1.0
github.com/davecgh/go-spew@v1.1.1
github.com/distribution/reference@v0.5.0
github.com/docker/docker@v25.0.5+incompatible
github.com/docker/go-connections@v0.5.0
github.com/docker/go-units@v0.5.0
github.com/elastic/elastic-transport-go/v8@v8.4.0
github.com/elastic/elastic-transport-go/v8@v8.5.0
github.com/elastic/go-elasticsearch/v8@v8.12.1
github.com/elastic/go-elasticsearch/v8@v8.13.1
github.com/felixge/httpsnoop@v1.0.4
github.com/go-logr/logr@v1.2.2
github.com/go-logr/logr@v1.3.0
github.com/go-logr/logr@v1.4.1
github.com/go-logr/stdr@v1.2.2
github.com/go-ole/go-ole@v1.2.6
github.com/gogo/protobuf@v1.3.2
github.com/golang/protobuf@v1.5.4
github.com/google/go-cmp@v0.5.6
github.com/google/go-cmp@v0.5.9
github.com/google/go-cmp@v0.6.0
github.com/google/uuid@v1.6.0
github.com/grpc-ecosystem/grpc-gateway/v2@v2.16.0
github.com/kisielk/errcheck@v1.5.0
github.com/kisielk/gotool@v1.0.0
github.com/klauspost/compress@v1.16.0
github.com/lufia/plan9stats@v0.0.0-20211012122336-39d0f177ccd0
github.com/magiconair/properties@v1.8.7
github.com/moby/patternmatcher@v0.6.0
github.com/moby/sys/sequential@v0.5.0
github.com/moby/sys/user@v0.1.0
github.com/moby/term@v0.5.0
github.com/morikuni/aec@v1.0.0
github.com/opencontainers/go-digest@v1.0.0
github.com/opencontainers/image-spec@v1.1.0
github.com/pkg/errors@v0.9.1
github.com/pmezard/go-difflib@v1.0.0
github.com/power-devops/perfstat@v0.0.0-20210106213030-5aafc221ea8c
github.com/shirou/gopsutil/v3@v3.23.12
github.com/shoenig/go-m1cpu@v0.1.6
github.com/shoenig/test@v0.6.4
github.com/sirupsen/logrus@v1.9.3
github.com/stretchr/objx@v0.1.0
github.com/stretchr/objx@v0.4.0
github.com/stretchr/objx@v0.5.0
github.com/stretchr/testify@v1.7.0
github.com/stretchr/testify@v1.7.1
github.com/stretchr/testify@v1.8.0
github.com/stretchr/testify@v1.8.4
github.com/stretchr/testify@v1.9.0
github.com/testcontainers/testcontainers-go/modules/elasticsearch@v0.31.0
github.com/testcontainers/testcontainers-go@v0.31.0
github.com/tklauser/go-sysconf@v0.3.12
github.com/tklauser/numcpus@v0.6.1
github.com/yuin/goldmark@v1.1.27
github.com/yuin/goldmark@v1.2.1
github.com/yusufpapurcu/wmi@v1.2.3
go.opentelemetry.io/contrib/instrumentation/net/http/otelhttp@v0.49.0
go.opentelemetry.io/otel/exporters/otlp/otlptrace/otlptracehttp@v1.19.0
go.opentelemetry.io/otel/exporters/otlp/otlptrace@v1.19.0
go.opentelemetry.io/otel/metric@v1.21.0
go.opentelemetry.io/otel/metric@v1.24.0
go.opentelemetry.io/otel/sdk@v1.21.0
go.opentelemetry.io/otel/trace@v1.21.0
go.opentelemetry.io/otel/trace@v1.24.0
go.opentelemetry.io/otel@v1.21.0
go.opentelemetry.io/otel@v1.24.0
go.opentelemetry.io/proto/otlp@v1.0.0
golang.org/x/crypto@v0.0.0-20190308221718-c2843e01d9a2
golang.org/x/crypto@v0.0.0-20191011191535-87dc89f01550
golang.org/x/crypto@v0.0.0-20200622213623-75b288015ac9
golang.org/x/crypto@v0.19.0
golang.org/x/crypto@v0.22.0
golang.org/x/mod@v0.15.0
golang.org/x/mod@v0.16.0
golang.org/x/mod@v0.2.0
golang.org/x/mod@v0.3.0
golang.org/x/net@v0.0.0-20190404232315-eb5bcb51f2a3
golang.org/x/net@v0.0.0-20190620200207-3b0461eec859
golang.org/x/net@v0.0.0-20200226121028-0de0cce0169b
golang.org/x/net@v0.0.0-20201021035429-f5854403a974
golang.org/x/net@v0.21.0
golang.org/x/sync@v0.0.0-20190423024810-112230192c58
golang.org/x/sync@v0.0.0-20190911185100-cd5d95a43a6e
golang.org/x/sync@v0.0.0-20201020160332-67f06af15bc9
golang.org/x/sync@v0.3.0
golang.org/x/sys@v0.0.0-20190215142949-d0b11bdaac8a
golang.org/x/sys@v0.0.0-20190412213103-97732733099d
golang.org/x/sys@v0.0.0-20190916202348-b4ddaad3f8a3
golang.org/x/sys@v0.0.0-20200930185726-fdedc70b468f
golang.org/x/sys@v0.0.0-20201204225414-ed752295db88
golang.org/x/sys@v0.0.0-20210616094352-59db8d763f22
golang.org/x/sys@v0.0.0-20220715151400-c0bba94af5f8
golang.org/x/sys@v0.11.0
golang.org/x/sys@v0.14.0
golang.org/x/sys@v0.15.0
golang.org/x/sys@v0.17.0
golang.org/x/sys@v0.18.0
golang.org/x/sys@v0.19.0
golang.org/x/sys@v0.8.0
golang.org/x/term@v0.17.0
golang.org/x/term@v0.19.0
golang.org/x/text@v0.14.0
golang.org/x/text@v0.3.0
golang.org/x/text@v0.3.3
golang.org/x/time@v0.3.0
golang.org/x/tools@v0.0.0-20180917221912-90fa682c2a6e
golang.org/x/tools@v0.0.0-20191119224855-298f0cb1881e
golang.org/x/tools@v0.0.0-20200619180055-7c47624df98f
golang.org/x/tools@v0.0.0-20210106214847-113979e3529a
golang.org/x/tools@v0.13.0
golang.org/x/tools@v0.18.0
golang.org/x/tools@v0.19.0
golang.org/x/xerrors@v0.0.0-20190717185122-a985d3407aa7
golang.org/x/xerrors@v0.0.0-20191011141410-1b5146add898
golang.org/x/xerrors@v0.0.0-20191204190536-9bdfabe68543
golang.org/x/xerrors@v0.0.0-20200804184101-5ec99f83aff1
google.golang.org/genproto/googleapis/api@v0.0.0-20230711160842-782d3b101e98
google.golang.org/genproto/googleapis/rpc@v0.0.0-20230731190214-cbb8c96f2d6d
google.golang.org/genproto@v0.0.0-20230711160842-782d3b101e98
google.golang.org/grpc@v1.58.3
google.golang.org/protobuf@v1.33.0
gopkg.in/check.v1@v0.0.0-20161208181325-20d25e280405
gopkg.in/yaml.v3@v3.0.0-20200313102051-9f266ea9e77c
gopkg.in/yaml.v3@v3.0.1
gotest.tools/v3@v3.5.0
__GOBAKE__
RUN set +e; cd /workspace/repo && d=$(dirname $(find . -name go.mod -not -path '*/vendor/*' 2>/dev/null | head -1)); [ -z "$d" ] && d=.; cd "$d"; while read -r c; do [ -n "$c" ] && GOWORK=off GOFLAGS=-mod=mod go mod download "$c" 2>/dev/null; done < /tmp/_gobake_coords.txt; true
ENV GOWORK=off
ENV GOFLAGS=-mod=mod
ENV GOPROXY=off
ENV GOSUMDB=off

# --- login-shell PATH: the client execs the verifier via `bash -lc`, which resets PATH from
# /etc/profile and drops the base-image toolchain (go/cargo/...). Bake the built PATH into
# /etc/profile.d so login shells find the toolchain (matches non-login Modal behavior). ---
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
