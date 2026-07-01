FROM golang:1.21

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/okteto/okteto.git && \
    git fetch --depth 30 origin 07a60636931919d240336465598ab46f0265d24a && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN go mod download || true


# --- offline dep-baking: warm Go module cache at build (network available here) ---
RUN cd /workspace/repo && \
    for d in $(find . -name go.mod -not -path '*/vendor/*' -exec dirname {} \; | sort -u); do \
      ( cd "$d" && echo "warming $d" && \
        (go mod download all 2>/dev/null || go mod download 2>/dev/null || true) && \
        (go test -count=1 -run '^$' ./... 2>/dev/null || true) ) ; \
    done; true
# warm exact dep versions the fix/test introduce (module coordinates only, not solution code)
RUN cd /workspace/repo && for m in \
    cloud.google.com/go/aiplatform@v1.22.0 \
    cloud.google.com/go/aiplatform@v1.24.0 \
    cloud.google.com/go/analytics@v0.11.0 \
    cloud.google.com/go/analytics@v0.12.0 \
    cloud.google.com/go/area120@v0.5.0 \
    cloud.google.com/go/area120@v0.6.0 \
    cloud.google.com/go/artifactregistry@v1.6.0 \
    cloud.google.com/go/artifactregistry@v1.7.0 \
    cloud.google.com/go/asset@v1.5.0 \
    cloud.google.com/go/asset@v1.7.0 \
    cloud.google.com/go/asset@v1.8.0 \
    cloud.google.com/go/assuredworkloads@v1.5.0 \
    cloud.google.com/go/assuredworkloads@v1.6.0 \
    cloud.google.com/go/assuredworkloads@v1.7.0 \
    cloud.google.com/go/automl@v1.5.0 \
    cloud.google.com/go/automl@v1.6.0 \
    cloud.google.com/go/bigquery@v1.42.0 \
    cloud.google.com/go/billing@v1.4.0 \
    cloud.google.com/go/billing@v1.5.0 \
    cloud.google.com/go/binaryauthorization@v1.1.0 \
    cloud.google.com/go/binaryauthorization@v1.2.0 \
    cloud.google.com/go/cloudtasks@v1.5.0 \
    cloud.google.com/go/cloudtasks@v1.6.0 \
    cloud.google.com/go/compute@v0.1.0 \
    cloud.google.com/go/compute@v1.10.0 \
    cloud.google.com/go/compute@v1.3.0 \
    cloud.google.com/go/compute@v1.5.0 \
    cloud.google.com/go/compute@v1.6.0 \
    cloud.google.com/go/compute@v1.6.1 \
    cloud.google.com/go/compute@v1.7.0 \
    cloud.google.com/go/containeranalysis@v0.5.1 \
    cloud.google.com/go/containeranalysis@v0.6.0 \
    cloud.google.com/go/datacatalog@v1.3.0 \
    cloud.google.com/go/datacatalog@v1.5.0 \
    cloud.google.com/go/datacatalog@v1.6.0 \
    cloud.google.com/go/dataflow@v0.6.0 \
    cloud.google.com/go/dataflow@v0.7.0 \
    cloud.google.com/go/dataform@v0.3.0 \
    cloud.google.com/go/dataform@v0.4.0 \
    cloud.google.com/go/datalabeling@v0.5.0 \
    cloud.google.com/go/datalabeling@v0.6.0 \
    cloud.google.com/go/dataqna@v0.5.0 \
    cloud.google.com/go/dataqna@v0.6.0 \
    cloud.google.com/go/datastream@v1.2.0 \
    cloud.google.com/go/datastream@v1.3.0 \
    cloud.google.com/go/dialogflow@v1.15.0 \
    cloud.google.com/go/dialogflow@v1.16.1 \
    cloud.google.com/go/dialogflow@v1.17.0 \
    cloud.google.com/go/documentai@v1.7.0 \
    cloud.google.com/go/documentai@v1.8.0 \
    cloud.google.com/go/domains@v0.6.0 \
    cloud.google.com/go/domains@v0.7.0 \
    cloud.google.com/go/edgecontainer@v0.1.0 \
    cloud.google.com/go/edgecontainer@v0.2.0 \
    cloud.google.com/go/functions@v1.6.0 \
    cloud.google.com/go/functions@v1.7.0 \
    cloud.google.com/go/gaming@v1.5.0 \
    cloud.google.com/go/gaming@v1.6.0 \
    cloud.google.com/go/gkeconnect@v0.5.0 \
    cloud.google.com/go/gkeconnect@v0.6.0 \
    cloud.google.com/go/gkehub@v0.10.0 \
    cloud.google.com/go/gkehub@v0.9.0 \
    cloud.google.com/go/grafeas@v0.2.0 \
    cloud.google.com/go/iam@v0.3.0 \
    cloud.google.com/go/iam@v0.5.0 \
    cloud.google.com/go/language@v1.4.0 \
    cloud.google.com/go/language@v1.6.0 \
    cloud.google.com/go/lifesciences@v0.5.0 \
    cloud.google.com/go/lifesciences@v0.6.0 \
    cloud.google.com/go/mediatranslation@v0.5.0 \
    cloud.google.com/go/mediatranslation@v0.6.0 \
    cloud.google.com/go/memcache@v1.4.0 \
    cloud.google.com/go/memcache@v1.5.0 \
    cloud.google.com/go/metastore@v1.5.0 \
    cloud.google.com/go/metastore@v1.6.0 \
    cloud.google.com/go/networkconnectivity@v1.4.0 \
    cloud.google.com/go/networkconnectivity@v1.5.0 \
    cloud.google.com/go/networksecurity@v0.5.0 \
    cloud.google.com/go/networksecurity@v0.6.0 \
    cloud.google.com/go/notebooks@v1.2.0 \
    cloud.google.com/go/notebooks@v1.3.0 \
    cloud.google.com/go/osconfig@v1.7.0 \
    cloud.google.com/go/osconfig@v1.8.0 \
    cloud.google.com/go/oslogin@v1.4.0 \
    cloud.google.com/go/oslogin@v1.5.0 \
    cloud.google.com/go/phishingprotection@v0.5.0 \
    cloud.google.com/go/phishingprotection@v0.6.0 \
    cloud.google.com/go/privatecatalog@v0.5.0 \
    cloud.google.com/go/privatecatalog@v0.6.0 \
    cloud.google.com/go/recaptchaenterprise/v2@v2.1.0 \
    cloud.google.com/go/recaptchaenterprise/v2@v2.2.0 \
    cloud.google.com/go/recaptchaenterprise/v2@v2.3.0 \
    cloud.google.com/go/recaptchaenterprise@v1.3.1 \
    cloud.google.com/go/recommendationengine@v0.5.0 \
    cloud.google.com/go/recommendationengine@v0.6.0 \
    cloud.google.com/go/recommender@v1.5.0 \
    cloud.google.com/go/recommender@v1.6.0 \
    cloud.google.com/go/redis@v1.7.0 \
    cloud.google.com/go/redis@v1.8.0 \
    cloud.google.com/go/retail@v1.8.0 \
    cloud.google.com/go/retail@v1.9.0 \
    cloud.google.com/go/scheduler@v1.4.0 \
    cloud.google.com/go/scheduler@v1.5.0 \
    cloud.google.com/go/secretmanager@v1.6.0 \
    cloud.google.com/go/security@v1.5.0 \
    cloud.google.com/go/security@v1.7.0 \
    cloud.google.com/go/security@v1.8.0 \
    cloud.google.com/go/securitycenter@v1.13.0 \
    cloud.google.com/go/securitycenter@v1.14.0 \
    cloud.google.com/go/servicedirectory@v1.4.0 \
    cloud.google.com/go/servicedirectory@v1.5.0 \
    cloud.google.com/go/speech@v1.6.0 \
    cloud.google.com/go/speech@v1.7.0 \
    cloud.google.com/go/storage@v1.22.1 \
    cloud.google.com/go/storage@v1.23.0 \
    cloud.google.com/go/storage@v1.27.0 \
    cloud.google.com/go/talent@v1.1.0 \
    cloud.google.com/go/talent@v1.2.0 \
    cloud.google.com/go/videointelligence@v1.6.0 \
    cloud.google.com/go/videointelligence@v1.7.0 \
    cloud.google.com/go/vision/v2@v2.2.0 \
    cloud.google.com/go/vision/v2@v2.3.0 \
    cloud.google.com/go/vision@v1.2.0 \
    cloud.google.com/go/webrisk@v1.4.0 \
    cloud.google.com/go/webrisk@v1.5.0 \
    cloud.google.com/go/workflows@v1.6.0 \
    cloud.google.com/go/workflows@v1.7.0 \
    cloud.google.com/go@v0.100.2 \
    cloud.google.com/go@v0.102.0 \
    cloud.google.com/go@v0.102.1 \
    cloud.google.com/go@v0.104.0 \
    github.com/aws/aws-sdk-go@v1.44.122 \
    github.com/chainguard-dev/git-urls@v1.0.2 \
    github.com/envoyproxy/go-control-plane@v0.10.2-0.20220325020618-49ff273808a1 \
    github.com/google/go-cmp@v0.5.8 \
    github.com/google/go-cmp@v0.5.9 \
    github.com/googleapis/enterprise-certificate-proxy@v0.0.0-20220520183353-fd19c99a87aa \
    github.com/googleapis/enterprise-certificate-proxy@v0.1.0 \
    github.com/googleapis/enterprise-certificate-proxy@v0.2.0 \
    github.com/googleapis/gax-go/v2@v2.2.0 \
    github.com/googleapis/gax-go/v2@v2.3.0 \
    github.com/googleapis/gax-go/v2@v2.4.0 \
    github.com/googleapis/gax-go/v2@v2.5.1 \
    github.com/googleapis/gax-go/v2@v2.6.0 \
    github.com/googleapis/go-type-adapters@v1.0.0 \
    github.com/hashicorp/go-getter@v1.7.3 \
    github.com/hashicorp/go-version@v1.6.0 \
    github.com/klauspost/compress@v1.15.11 \
    github.com/mitchellh/go-testing-interface@v1.14.1 \
    github.com/spf13/pflag@v1.0.5 \
    github.com/ulikunitz/xz@v0.5.10 \
    golang.org/x/net@v0.0.0-20220127200216-cd36cc0744dd \
    golang.org/x/net@v0.0.0-20220225172249-27dd8689420f \
    golang.org/x/net@v0.0.0-20220325170049-de3da57026de \
    golang.org/x/net@v0.0.0-20220412020605-290c469a71a5 \
    golang.org/x/net@v0.0.0-20220425223048-2871e0cb64e4 \
    golang.org/x/net@v0.0.0-20220607020251-c690dde0001d \
    golang.org/x/net@v0.0.0-20220617184016-355a448f1bc9 \
    golang.org/x/net@v0.0.0-20220624214902-1bab6f366d9e \
    golang.org/x/net@v0.0.0-20220909164309-bea034e7d591 \
    golang.org/x/net@v0.0.0-20221014081412-f15817d10f9b \
    golang.org/x/oauth2@v0.0.0-20220223155221-ee480838109b \
    golang.org/x/oauth2@v0.0.0-20220309155454-6242fa91716a \
    golang.org/x/oauth2@v0.0.0-20220411215720-9780585627b5 \
    golang.org/x/oauth2@v0.0.0-20220608161450-d0670ef3b1eb \
    golang.org/x/oauth2@v0.0.0-20220622183110-fd043fe589d2 \
    golang.org/x/oauth2@v0.0.0-20220822191816-0ebed06d0094 \
    golang.org/x/oauth2@v0.0.0-20220909003341-f21342109be1 \
    golang.org/x/oauth2@v0.0.0-20221014153046-6fdb5e3db783 \
    golang.org/x/oauth2@v0.1.0 \
    golang.org/x/sync@v0.0.0-20220601150217-0de741cfad7f \
    golang.org/x/sync@v0.0.0-20220929204114-8fcdb60fdcc0 \
    golang.org/x/sys@v0.0.0-20211210111614-af8b64212486 \
    golang.org/x/sys@v0.0.0-20220128215802-99c3d69c2c27 \
    golang.org/x/sys@v0.0.0-20220209214540-3681064d5158 \
    golang.org/x/sys@v0.0.0-20220227234510-4e6760a101f9 \
    golang.org/x/sys@v0.0.0-20220328115105-d36c6a25d886 \
    golang.org/x/sys@v0.0.0-20220412211240-33da011f77ad \
    golang.org/x/sys@v0.0.0-20220502124256-b6088ccd6cba \
    golang.org/x/sys@v0.0.0-20220610221304-9f5ed59c137d \
    golang.org/x/sys@v0.0.0-20220615213510-4f61da869c0c \
    golang.org/x/sys@v0.0.0-20220624220833-87e55d714810 \
    golang.org/x/xerrors@v0.0.0-20220411194840-2f41105eb62f \
    golang.org/x/xerrors@v0.0.0-20220517211312-f3a8303e98df \
    golang.org/x/xerrors@v0.0.0-20220609144429-65e65417b02f \
    google.golang.org/api@v0.100.0 \
    google.golang.org/api@v0.63.0 \
    google.golang.org/api@v0.67.0 \
    google.golang.org/api@v0.70.0 \
    google.golang.org/api@v0.71.0 \
    google.golang.org/api@v0.74.0 \
    google.golang.org/api@v0.75.0 \
    google.golang.org/api@v0.77.0 \
    google.golang.org/api@v0.78.0 \
    google.golang.org/api@v0.80.0 \
    google.golang.org/api@v0.84.0 \
    google.golang.org/api@v0.85.0 \
    google.golang.org/api@v0.90.0 \
    google.golang.org/api@v0.93.0 \
    google.golang.org/api@v0.95.0 \
    google.golang.org/api@v0.96.0 \
    google.golang.org/api@v0.97.0 \
    google.golang.org/api@v0.98.0 \
    google.golang.org/genproto@v0.0.0-20210329143202-679c6ae281ee \
    google.golang.org/genproto@v0.0.0-20211221195035-429b39de9b1c \
    google.golang.org/genproto@v0.0.0-20220126215142-9970aeb2e350 \
    google.golang.org/genproto@v0.0.0-20220207164111-0872dc986b00 \
    google.golang.org/genproto@v0.0.0-20220218161850-94dd64e39d7c \
    google.golang.org/genproto@v0.0.0-20220222213610-43724f9ea8cf \
    google.golang.org/genproto@v0.0.0-20220304144024-325a89244dc8 \
    google.golang.org/genproto@v0.0.0-20220310185008-1973136f34c6 \
    google.golang.org/genproto@v0.0.0-20220324131243-acbaeb5b85eb \
    google.golang.org/genproto@v0.0.0-20220407144326-9054f6ed7bac \
    google.golang.org/genproto@v0.0.0-20220413183235-5e96e2839df9 \
    google.golang.org/genproto@v0.0.0-20220414192740-2d67ff6cf2b4 \
    google.golang.org/genproto@v0.0.0-20220421151946-72621c1f0bd3 \
    google.golang.org/genproto@v0.0.0-20220429170224-98d788798c3e \
    google.golang.org/genproto@v0.0.0-20220502173005-c8bf987b8c21 \
    google.golang.org/genproto@v0.0.0-20220505152158-f39f71e6c8f3 \
    google.golang.org/genproto@v0.0.0-20220518221133-4f43b3371335 \
    google.golang.org/genproto@v0.0.0-20220523171625-347a074981d8 \
    google.golang.org/genproto@v0.0.0-20220608133413-ed9918b62aac \
    google.golang.org/genproto@v0.0.0-20220616135557-88e70c0c3a90 \
    google.golang.org/genproto@v0.0.0-20220617124728-180714bec0ad \
    google.golang.org/genproto@v0.0.0-20220624142145-8cd45d7dbd1f \
    google.golang.org/genproto@v0.0.0-20220628213854-d9e0b6570c03 \
    google.golang.org/genproto@v0.0.0-20220722212130-b98a9ff5e252 \
    google.golang.org/genproto@v0.0.0-20220801145646-83ce21fca29f \
    google.golang.org/genproto@v0.0.0-20220815135757-37a418bb8959 \
    google.golang.org/genproto@v0.0.0-20220817144833-d7fd3f11b9b1 \
    google.golang.org/genproto@v0.0.0-20220822174746-9e6da59bd2fc \
    google.golang.org/genproto@v0.0.0-20220829144015-23454907ede3 \
    google.golang.org/genproto@v0.0.0-20220829175752-36a9c930ecbf \
    google.golang.org/genproto@v0.0.0-20220913154956-18f8339a66a5 \
    google.golang.org/genproto@v0.0.0-20220914142337-ca0e39ece12f \
    google.golang.org/genproto@v0.0.0-20220915135415-7fd63a7952de \
    google.golang.org/genproto@v0.0.0-20220916172020-2692e8806bfa \
    google.golang.org/genproto@v0.0.0-20220919141832-68c03719ef51 \
    google.golang.org/genproto@v0.0.0-20220920201722-2b89144ce006 \
    google.golang.org/genproto@v0.0.0-20220926165614-551eb538f295 \
    google.golang.org/genproto@v0.0.0-20220926220553-6981cbe3cfce \
    google.golang.org/genproto@v0.0.0-20221010155953-15ba04fc1c0e \
    google.golang.org/genproto@v0.0.0-20221014173430-6e2ab493f96b \
    google.golang.org/genproto@v0.0.0-20221014213838-99cd37c6964a \
    google.golang.org/genproto@v0.0.0-20221025140454-527a21cfbd71 \
    google.golang.org/grpc@v1.46.0 \
    google.golang.org/grpc@v1.46.2 \
    google.golang.org/grpc@v1.47.0 \
    google.golang.org/grpc@v1.48.0 \
    google.golang.org/grpc@v1.49.0 \
    google.golang.org/grpc@v1.50.0 \
    google.golang.org/grpc@v1.50.1 \
    google.golang.org/protobuf@v1.28.0 \
    google.golang.org/protobuf@v1.28.1 \
    ; do go mod download "$m" 2>/dev/null || true; done; true
ENV GOFLAGS=-mod=mod
ENV GOPROXY=off
ENV GOSUMDB=off

WORKDIR /workspace

# --- offline-harbor: ensure grading(python3) + patch(patch/git) deps (build-time) ---
RUN (command -v python3 >/dev/null 2>&1 && command -v patch >/dev/null 2>&1 && command -v git >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends python3 patch git ca-certificates) \
    || (apk add --no-cache python3 patch git ca-certificates) \
    || (dnf install -y python3 patch git ca-certificates) \
    || (yum install -y python3 patch git ca-certificates) \
    || true

# --- terminal-bench eval harness deps (tmux + asciinema), baked for offline eval ---
RUN (command -v tmux >/dev/null 2>&1 && command -v asciinema >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux asciinema) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux python3-pip && pip3 install --no-cache-dir asciinema) \
    || (apk add --no-cache tmux asciinema) \
    || (dnf install -y tmux asciinema) \
    || (yum install -y tmux asciinema) \
    || true

# --- login-shell PATH: the client execs the verifier via `bash -lc`, which resets PATH from
# /etc/profile and drops the base-image toolchain (go/cargo/...). Bake the built PATH into
# /etc/profile.d so login shells find the toolchain (matches non-login Modal behavior). ---
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
