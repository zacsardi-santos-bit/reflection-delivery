FROM golang:1.21

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/radius-project/radius.git && \
    git fetch --depth 30 origin 0a1a8680dace317f8bd25bdde2b503f895766d2d && \
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
RUN set +e; cd /workspace/repo 2>/dev/null || cd /workspace/repo; GOWORK=off GOFLAGS=-mod=mod go test -count=1 -run '^$' ./pkg/recipes/terraform 2>/dev/null; true
RUN cat > /tmp/_gobake_coords.txt <<'__GOBAKE__'
cloud.google.com/go/aiplatform@v1.22.0
cloud.google.com/go/aiplatform@v1.24.0
cloud.google.com/go/analytics@v0.11.0
cloud.google.com/go/analytics@v0.12.0
cloud.google.com/go/area120@v0.5.0
cloud.google.com/go/area120@v0.6.0
cloud.google.com/go/artifactregistry@v1.6.0
cloud.google.com/go/artifactregistry@v1.7.0
cloud.google.com/go/asset@v1.5.0
cloud.google.com/go/asset@v1.7.0
cloud.google.com/go/asset@v1.8.0
cloud.google.com/go/assuredworkloads@v1.5.0
cloud.google.com/go/assuredworkloads@v1.6.0
cloud.google.com/go/assuredworkloads@v1.7.0
cloud.google.com/go/automl@v1.5.0
cloud.google.com/go/automl@v1.6.0
cloud.google.com/go/bigquery@v1.42.0
cloud.google.com/go/billing@v1.4.0
cloud.google.com/go/billing@v1.5.0
cloud.google.com/go/binaryauthorization@v1.1.0
cloud.google.com/go/binaryauthorization@v1.2.0
cloud.google.com/go/cloudtasks@v1.5.0
cloud.google.com/go/cloudtasks@v1.6.0
cloud.google.com/go/compute/metadata@v0.2.3
cloud.google.com/go/compute@v0.1.0
cloud.google.com/go/compute@v1.10.0
cloud.google.com/go/compute@v1.20.1
cloud.google.com/go/compute@v1.3.0
cloud.google.com/go/compute@v1.5.0
cloud.google.com/go/compute@v1.6.0
cloud.google.com/go/compute@v1.6.1
cloud.google.com/go/compute@v1.7.0
cloud.google.com/go/containeranalysis@v0.5.1
cloud.google.com/go/containeranalysis@v0.6.0
cloud.google.com/go/datacatalog@v1.3.0
cloud.google.com/go/datacatalog@v1.5.0
cloud.google.com/go/datacatalog@v1.6.0
cloud.google.com/go/dataflow@v0.6.0
cloud.google.com/go/dataflow@v0.7.0
cloud.google.com/go/dataform@v0.3.0
cloud.google.com/go/dataform@v0.4.0
cloud.google.com/go/datalabeling@v0.5.0
cloud.google.com/go/datalabeling@v0.6.0
cloud.google.com/go/dataqna@v0.5.0
cloud.google.com/go/dataqna@v0.6.0
cloud.google.com/go/datastream@v1.2.0
cloud.google.com/go/datastream@v1.3.0
cloud.google.com/go/dialogflow@v1.15.0
cloud.google.com/go/dialogflow@v1.16.1
cloud.google.com/go/dialogflow@v1.17.0
cloud.google.com/go/documentai@v1.7.0
cloud.google.com/go/documentai@v1.8.0
cloud.google.com/go/domains@v0.6.0
cloud.google.com/go/domains@v0.7.0
cloud.google.com/go/edgecontainer@v0.1.0
cloud.google.com/go/edgecontainer@v0.2.0
cloud.google.com/go/functions@v1.6.0
cloud.google.com/go/functions@v1.7.0
cloud.google.com/go/gaming@v1.5.0
cloud.google.com/go/gaming@v1.6.0
cloud.google.com/go/gkeconnect@v0.5.0
cloud.google.com/go/gkeconnect@v0.6.0
cloud.google.com/go/gkehub@v0.10.0
cloud.google.com/go/gkehub@v0.9.0
cloud.google.com/go/grafeas@v0.2.0
cloud.google.com/go/iam@v0.3.0
cloud.google.com/go/iam@v0.5.0
cloud.google.com/go/iam@v1.1.1
cloud.google.com/go/language@v1.4.0
cloud.google.com/go/language@v1.6.0
cloud.google.com/go/lifesciences@v0.5.0
cloud.google.com/go/lifesciences@v0.6.0
cloud.google.com/go/mediatranslation@v0.5.0
cloud.google.com/go/mediatranslation@v0.6.0
cloud.google.com/go/memcache@v1.4.0
cloud.google.com/go/memcache@v1.5.0
cloud.google.com/go/metastore@v1.5.0
cloud.google.com/go/metastore@v1.6.0
cloud.google.com/go/networkconnectivity@v1.4.0
cloud.google.com/go/networkconnectivity@v1.5.0
cloud.google.com/go/networksecurity@v0.5.0
cloud.google.com/go/networksecurity@v0.6.0
cloud.google.com/go/notebooks@v1.2.0
cloud.google.com/go/notebooks@v1.3.0
cloud.google.com/go/osconfig@v1.7.0
cloud.google.com/go/osconfig@v1.8.0
cloud.google.com/go/oslogin@v1.4.0
cloud.google.com/go/oslogin@v1.5.0
cloud.google.com/go/phishingprotection@v0.5.0
cloud.google.com/go/phishingprotection@v0.6.0
cloud.google.com/go/privatecatalog@v0.5.0
cloud.google.com/go/privatecatalog@v0.6.0
cloud.google.com/go/recaptchaenterprise/v2@v2.1.0
cloud.google.com/go/recaptchaenterprise/v2@v2.2.0
cloud.google.com/go/recaptchaenterprise/v2@v2.3.0
cloud.google.com/go/recaptchaenterprise@v1.3.1
cloud.google.com/go/recommendationengine@v0.5.0
cloud.google.com/go/recommendationengine@v0.6.0
cloud.google.com/go/recommender@v1.5.0
cloud.google.com/go/recommender@v1.6.0
cloud.google.com/go/redis@v1.7.0
cloud.google.com/go/redis@v1.8.0
cloud.google.com/go/retail@v1.8.0
cloud.google.com/go/retail@v1.9.0
cloud.google.com/go/scheduler@v1.4.0
cloud.google.com/go/scheduler@v1.5.0
cloud.google.com/go/secretmanager@v1.6.0
cloud.google.com/go/security@v1.5.0
cloud.google.com/go/security@v1.7.0
cloud.google.com/go/security@v1.8.0
cloud.google.com/go/securitycenter@v1.13.0
cloud.google.com/go/securitycenter@v1.14.0
cloud.google.com/go/servicedirectory@v1.4.0
cloud.google.com/go/servicedirectory@v1.5.0
cloud.google.com/go/speech@v1.6.0
cloud.google.com/go/speech@v1.7.0
cloud.google.com/go/storage@v1.22.1
cloud.google.com/go/storage@v1.23.0
cloud.google.com/go/storage@v1.27.0
cloud.google.com/go/storage@v1.30.1
cloud.google.com/go/talent@v1.1.0
cloud.google.com/go/talent@v1.2.0
cloud.google.com/go/videointelligence@v1.6.0
cloud.google.com/go/videointelligence@v1.7.0
cloud.google.com/go/vision/v2@v2.2.0
cloud.google.com/go/vision/v2@v2.3.0
cloud.google.com/go/vision@v1.2.0
cloud.google.com/go/webrisk@v1.4.0
cloud.google.com/go/webrisk@v1.5.0
cloud.google.com/go/workflows@v1.6.0
cloud.google.com/go/workflows@v1.7.0
cloud.google.com/go@v0.100.2
cloud.google.com/go@v0.102.0
cloud.google.com/go@v0.102.1
cloud.google.com/go@v0.104.0
cloud.google.com/go@v0.110.4
cloud.google.com/go@v0.83.0
cloud.google.com/go@v0.84.0
cloud.google.com/go@v0.87.0
cloud.google.com/go@v0.90.0
cloud.google.com/go@v0.93.3
cloud.google.com/go@v0.94.1
cloud.google.com/go@v0.97.0
cloud.google.com/go@v0.99.0
github.com/aws/aws-sdk-go@v1.44.122
github.com/bgentry/go-netrc@v0.0.0-20140422174119-9fd32a8b3d3d
github.com/cespare/xxhash/v2@v2.1.1
github.com/cheggaaa/pb@v1.0.27
github.com/cncf/udpa/go@v0.0.0-20210930031921-04548b0d99d4
github.com/cncf/xds/go@v0.0.0-20210805033703-aa0b78936158
github.com/cncf/xds/go@v0.0.0-20210922020428-25de7278fc84
github.com/cncf/xds/go@v0.0.0-20211001041855-01bcc9b48dfe
github.com/cncf/xds/go@v0.0.0-20211011173535-cb28da3451f1
github.com/envoyproxy/go-control-plane@v0.10.2-0.20220325020618-49ff273808a1
github.com/envoyproxy/go-control-plane@v0.9.10-0.20210907150352-cf90f659a021
github.com/golang/snappy@v0.0.3
github.com/google/go-cmp@v0.5.6
github.com/google/martian/v3@v3.2.1
github.com/google/martian/v3@v3.3.2
github.com/google/martian@v2.1.0+incompatible
github.com/google/pprof@v0.0.0-20210601050228-01bbb1931b22
github.com/google/pprof@v0.0.0-20210609004039-a478d1d731e9
github.com/google/pprof@v0.0.0-20210720184732-4bb14d4b1be1
github.com/google/s2a-go@v0.1.4
github.com/googleapis/enterprise-certificate-proxy@v0.0.0-20220520183353-fd19c99a87aa
github.com/googleapis/enterprise-certificate-proxy@v0.1.0
github.com/googleapis/enterprise-certificate-proxy@v0.2.0
github.com/googleapis/enterprise-certificate-proxy@v0.2.3
github.com/googleapis/gax-go/v2@v2.1.0
github.com/googleapis/gax-go/v2@v2.1.1
github.com/googleapis/gax-go/v2@v2.11.0
github.com/googleapis/gax-go/v2@v2.2.0
github.com/googleapis/gax-go/v2@v2.3.0
github.com/googleapis/gax-go/v2@v2.4.0
github.com/googleapis/gax-go/v2@v2.5.1
github.com/googleapis/gax-go/v2@v2.6.0
github.com/googleapis/go-type-adapters@v1.0.0
github.com/hashicorp/go-getter@v1.7.3
github.com/hashicorp/go-hclog@v1.2.0
github.com/hashicorp/go-retryablehttp@v0.7.5
github.com/hashicorp/go-safetemp@v1.0.0
github.com/klauspost/compress@v1.15.11
github.com/mattn/go-isatty@v0.0.4
github.com/mattn/go-runewidth@v0.0.4
github.com/mitchellh/go-homedir@v1.1.0
github.com/mitchellh/go-testing-interface@v1.14.1
github.com/ulikunitz/xz@v0.5.10
go.opencensus.io@v0.24.0
golang.org/x/crypto@v0.0.0-20220314234659-1baeb1ce4c0b
golang.org/x/net@v0.0.0-20210503060351-7fd8e65b6420
golang.org/x/net@v0.0.0-20220127200216-cd36cc0744dd
golang.org/x/net@v0.0.0-20220225172249-27dd8689420f
golang.org/x/net@v0.0.0-20220325170049-de3da57026de
golang.org/x/net@v0.0.0-20220412020605-290c469a71a5
golang.org/x/net@v0.0.0-20220425223048-2871e0cb64e4
golang.org/x/net@v0.0.0-20220607020251-c690dde0001d
golang.org/x/net@v0.0.0-20220617184016-355a448f1bc9
golang.org/x/net@v0.0.0-20220624214902-1bab6f366d9e
golang.org/x/net@v0.0.0-20220909164309-bea034e7d591
golang.org/x/net@v0.0.0-20221014081412-f15817d10f9b
golang.org/x/net@v0.1.0
golang.org/x/oauth2@v0.0.0-20210514164344-f6687ab2804c
golang.org/x/oauth2@v0.0.0-20210628180205-a41e5a781914
golang.org/x/oauth2@v0.0.0-20210805134026-6f1e6394065a
golang.org/x/oauth2@v0.0.0-20210819190943-2bc19b11175f
golang.org/x/oauth2@v0.0.0-20211104180415-d3ed0bb246c8
golang.org/x/oauth2@v0.0.0-20220223155221-ee480838109b
golang.org/x/oauth2@v0.0.0-20220309155454-6242fa91716a
golang.org/x/oauth2@v0.0.0-20220411215720-9780585627b5
golang.org/x/oauth2@v0.0.0-20220608161450-d0670ef3b1eb
golang.org/x/oauth2@v0.0.0-20220622183110-fd043fe589d2
golang.org/x/oauth2@v0.0.0-20220822191816-0ebed06d0094
golang.org/x/oauth2@v0.0.0-20220909003341-f21342109be1
golang.org/x/oauth2@v0.0.0-20221014153046-6fdb5e3db783
golang.org/x/oauth2@v0.1.0
golang.org/x/sync@v0.0.0-20220601150217-0de741cfad7f
golang.org/x/sync@v0.0.0-20220929204114-8fcdb60fdcc0
golang.org/x/sys@v0.0.0-20210514084401-e8d321eab015
golang.org/x/sys@v0.0.0-20210603125802-9665404d3644
golang.org/x/sys@v0.0.0-20210806184541-e5e7981a1069
golang.org/x/sys@v0.0.0-20210823070655-63515b42dcdf
golang.org/x/sys@v0.0.0-20210908233432-aa78b53d3365
golang.org/x/sys@v0.0.0-20211124211545-fe61309f8881
golang.org/x/sys@v0.0.0-20211210111614-af8b64212486
golang.org/x/sys@v0.0.0-20211216021012-1d35b9e2eb4e
golang.org/x/sys@v0.0.0-20220128215802-99c3d69c2c27
golang.org/x/sys@v0.0.0-20220209214540-3681064d5158
golang.org/x/sys@v0.0.0-20220227234510-4e6760a101f9
golang.org/x/sys@v0.0.0-20220328115105-d36c6a25d886
golang.org/x/sys@v0.0.0-20220412211240-33da011f77ad
golang.org/x/sys@v0.0.0-20220502124256-b6088ccd6cba
golang.org/x/sys@v0.0.0-20220503163025-988cb79eb6c6
golang.org/x/sys@v0.0.0-20220610221304-9f5ed59c137d
golang.org/x/sys@v0.0.0-20220615213510-4f61da869c0c
golang.org/x/sys@v0.0.0-20220624220833-87e55d714810
golang.org/x/sys@v0.0.0-20220728004956-3c1f35247d10
golang.org/x/term@v0.1.0
golang.org/x/tools@v0.1.3
golang.org/x/tools@v0.1.4
golang.org/x/xerrors@v0.0.0-20220411194840-2f41105eb62f
golang.org/x/xerrors@v0.0.0-20220517211312-f3a8303e98df
golang.org/x/xerrors@v0.0.0-20220609144429-65e65417b02f
golang.org/x/xerrors@v0.0.0-20220907171357-04be3eba64a2
google.golang.org/api@v0.100.0
google.golang.org/api@v0.126.0
google.golang.org/api@v0.47.0
google.golang.org/api@v0.48.0
google.golang.org/api@v0.50.0
google.golang.org/api@v0.51.0
google.golang.org/api@v0.54.0
google.golang.org/api@v0.55.0
google.golang.org/api@v0.56.0
google.golang.org/api@v0.57.0
google.golang.org/api@v0.61.0
google.golang.org/api@v0.63.0
google.golang.org/api@v0.67.0
google.golang.org/api@v0.70.0
google.golang.org/api@v0.71.0
google.golang.org/api@v0.74.0
google.golang.org/api@v0.75.0
google.golang.org/api@v0.77.0
google.golang.org/api@v0.78.0
google.golang.org/api@v0.80.0
google.golang.org/api@v0.84.0
google.golang.org/api@v0.85.0
google.golang.org/api@v0.90.0
google.golang.org/api@v0.93.0
google.golang.org/api@v0.95.0
google.golang.org/api@v0.96.0
google.golang.org/api@v0.97.0
google.golang.org/api@v0.98.0
google.golang.org/genproto@v0.0.0-20210329143202-679c6ae281ee
google.golang.org/genproto@v0.0.0-20210513213006-bf773b8c8384
google.golang.org/genproto@v0.0.0-20210604141403-392c879c8b08
google.golang.org/genproto@v0.0.0-20210608205507-b6d2f5bf0d7d
google.golang.org/genproto@v0.0.0-20210624195500-8bfb893ecb84
google.golang.org/genproto@v0.0.0-20210713002101-d411969a0d9a
google.golang.org/genproto@v0.0.0-20210716133855-ce7ef5c701ea
google.golang.org/genproto@v0.0.0-20210728212813-7823e685a01f
google.golang.org/genproto@v0.0.0-20210805201207-89edb61ffb67
google.golang.org/genproto@v0.0.0-20210813162853-db860fec028c
google.golang.org/genproto@v0.0.0-20210821163610-241b8fcbd6c8
google.golang.org/genproto@v0.0.0-20210828152312-66f60bf46e71
google.golang.org/genproto@v0.0.0-20210831024726-fe130286e0e2
google.golang.org/genproto@v0.0.0-20210903162649-d08c68adba83
google.golang.org/genproto@v0.0.0-20210909211513-a8c4777a87af
google.golang.org/genproto@v0.0.0-20210924002016-3dee208752a0
google.golang.org/genproto@v0.0.0-20211118181313-81c1377c94b1
google.golang.org/genproto@v0.0.0-20211206160659-862468c7d6e0
google.golang.org/genproto@v0.0.0-20211208223120-3a66f561d7aa
google.golang.org/genproto@v0.0.0-20211221195035-429b39de9b1c
google.golang.org/genproto@v0.0.0-20220126215142-9970aeb2e350
google.golang.org/genproto@v0.0.0-20220207164111-0872dc986b00
google.golang.org/genproto@v0.0.0-20220218161850-94dd64e39d7c
google.golang.org/genproto@v0.0.0-20220222213610-43724f9ea8cf
google.golang.org/genproto@v0.0.0-20220304144024-325a89244dc8
google.golang.org/genproto@v0.0.0-20220310185008-1973136f34c6
google.golang.org/genproto@v0.0.0-20220324131243-acbaeb5b85eb
google.golang.org/genproto@v0.0.0-20220407144326-9054f6ed7bac
google.golang.org/genproto@v0.0.0-20220413183235-5e96e2839df9
google.golang.org/genproto@v0.0.0-20220414192740-2d67ff6cf2b4
google.golang.org/genproto@v0.0.0-20220421151946-72621c1f0bd3
google.golang.org/genproto@v0.0.0-20220429170224-98d788798c3e
google.golang.org/genproto@v0.0.0-20220502173005-c8bf987b8c21
google.golang.org/genproto@v0.0.0-20220505152158-f39f71e6c8f3
google.golang.org/genproto@v0.0.0-20220518221133-4f43b3371335
google.golang.org/genproto@v0.0.0-20220523171625-347a074981d8
google.golang.org/genproto@v0.0.0-20220608133413-ed9918b62aac
google.golang.org/genproto@v0.0.0-20220616135557-88e70c0c3a90
google.golang.org/genproto@v0.0.0-20220617124728-180714bec0ad
google.golang.org/genproto@v0.0.0-20220624142145-8cd45d7dbd1f
google.golang.org/genproto@v0.0.0-20220628213854-d9e0b6570c03
google.golang.org/genproto@v0.0.0-20220722212130-b98a9ff5e252
google.golang.org/genproto@v0.0.0-20220801145646-83ce21fca29f
google.golang.org/genproto@v0.0.0-20220815135757-37a418bb8959
google.golang.org/genproto@v0.0.0-20220817144833-d7fd3f11b9b1
google.golang.org/genproto@v0.0.0-20220822174746-9e6da59bd2fc
google.golang.org/genproto@v0.0.0-20220829144015-23454907ede3
google.golang.org/genproto@v0.0.0-20220829175752-36a9c930ecbf
google.golang.org/genproto@v0.0.0-20220913154956-18f8339a66a5
google.golang.org/genproto@v0.0.0-20220914142337-ca0e39ece12f
google.golang.org/genproto@v0.0.0-20220915135415-7fd63a7952de
google.golang.org/genproto@v0.0.0-20220916172020-2692e8806bfa
google.golang.org/genproto@v0.0.0-20220919141832-68c03719ef51
google.golang.org/genproto@v0.0.0-20220920201722-2b89144ce006
google.golang.org/genproto@v0.0.0-20220926165614-551eb538f295
google.golang.org/genproto@v0.0.0-20220926220553-6981cbe3cfce
google.golang.org/genproto@v0.0.0-20221010155953-15ba04fc1c0e
google.golang.org/genproto@v0.0.0-20221014173430-6e2ab493f96b
google.golang.org/genproto@v0.0.0-20221014213838-99cd37c6964a
google.golang.org/genproto@v0.0.0-20221025140454-527a21cfbd71
google.golang.org/grpc/cmd/protoc-gen-go-grpc@v1.1.0
google.golang.org/grpc@v1.37.0
google.golang.org/grpc@v1.37.1
google.golang.org/grpc@v1.39.0
google.golang.org/grpc@v1.39.1
google.golang.org/grpc@v1.40.1
google.golang.org/grpc@v1.44.0
google.golang.org/grpc@v1.45.0
google.golang.org/grpc@v1.46.0
google.golang.org/grpc@v1.46.2
google.golang.org/grpc@v1.47.0
google.golang.org/grpc@v1.48.0
google.golang.org/grpc@v1.49.0
google.golang.org/grpc@v1.50.0
google.golang.org/grpc@v1.50.1
google.golang.org/protobuf@v1.28.0
google.golang.org/protobuf@v1.28.1
gopkg.in/cheggaaa/pb.v1@v1.0.27
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
