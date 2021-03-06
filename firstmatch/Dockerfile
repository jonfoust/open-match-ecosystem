# Copyright 2019 Google LLC
# 
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
# 
#     http://www.apache.org/licenses/LICENSE-2.0
# 
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

FROM golang:1.13.1 as builder
ENV GO111MODULE=on

WORKDIR /go/src/open-match.dev/open-match-ecosystem/firstmatch

# First copy only the go.sum and go.mod then download dependencies. Docker
# caching is [in]validated by the input files changes.  So when the dependencies
# for the project don't change, the previous image layer can be re-used.  go.sum
# is included as its hashing verifies the expected files are downloaded.
COPY firstmatch/go.mod firstmatch/go.sum ./
COPY demoui/go.mod demoui/go.sum ../demoui/

RUN go mod download

COPY . ..

RUN mkdir -p /app/

RUN CGO_ENABLED=0 go build -a -installsuffix cgo -o /app/run open-match.dev/open-match-ecosystem/firstmatch

RUN cp -r ../demoui/static /app/static

FROM gcr.io/distroless/static:nonroot
WORKDIR /app/

COPY --from=builder --chown=nonroot "/app/" "/app/"

ENTRYPOINT ["/app/run"]
