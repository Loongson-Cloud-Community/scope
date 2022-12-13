FROM cr.loongnix.cn/library/golang:1.15
ENV SCOPE_SKIP_UI_ASSETS true

RUN set -eux; \
   export arch_val="$(dpkg --print-architecture)"; \
   apt-get update && \
   if [ "$arch_val" = "amd64" ]; then \
     apt-get install -y libpcap-dev time file shellcheck git gcc-arm-linux-gnueabihf curl build-essential python-pip; \
   elif [ "$arch_val" = "loongarch64" ]; then \
     apt-get install -y libpcap-dev time file git curl build-essential python-pip docker-ce; \
   else \
     apt-get install -y libpcap-dev time file shellcheck git curl build-essential python-pip; \
   fi; \
   \
   rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

RUN go clean -i net && \
	go install -tags netgo std && \
   export arch_val="$(dpkg --print-architecture)"; \
   if [ "$arch_val" != "ppc64el" ]; then \
	go install -tags netgo std; \
   fi; \
   export GO111MODULE=on && go get -tags netgo \
		github.com/fzipp/gocyclo@ffe36aa317dcbb421a536de071660261136174dd \
		golang.org/x/lint/golint \
		github.com/kisielk/errcheck \
		github.com/fatih/hclfmt \
		github.com/mjibson/esc \
		github.com/client9/misspell/cmd/misspell && \
	chmod a+wr --recursive /usr/local/go && \
	rm -rf /go/pkg/ /go/src/

   # Only install shfmt on amd64, as the version v1.3.0 isn't supported for ppc64le
   # and the later version of shfmt doesn't work with the application well
RUN export arch_val="$(dpkg --print-architecture)"; \
   if [ "$arch_val" = "amd64" ]; then \
     curl -fsSL -o shfmt https://github.com/mvdan/sh/releases/download/v1.3.0/shfmt_v1.3.0_linux_amd64 && \
     chmod +x shfmt && \
     mv shfmt /usr/bin; \
   fi;

RUN pip install yapf==0.16.2 flake8==3.3.0 requests==2.19.1

COPY build.sh /
ENTRYPOINT ["/build.sh"]

ARG revision
LABEL maintainer="zhaixiaojuan <zhaixiaojuan@loongson.cn>" \
      org.opencontainers.image.title="backend" \
      org.opencontainers.image.source="https://github.com/weaveworks/scope/tree/master/backend" \
      org.opencontainers.image.revision="${revision}" \
      org.opencontainers.image.vendor="Weaveworks"
