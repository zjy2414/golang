FROM tencentos/tencentos4-microdnf:latest

ENV GOLANG_VERSION=1.25.4

# don't auto-upgrade the gotoolchain
# https://github.com/docker-library/golang/issues/472
ENV GOTOOLCHAIN=local

ENV GOPATH=/go
ENV PATH=$GOPATH/bin:/usr/local/go/bin:$PATH

# Install minimal dependencies and Go
RUN set -eux; \
	microdnf install -y \
		ca-certificates \
		gzip \
		tar \
		wget \
	; \
	arch="$(uname -m)"; \
	url=; \
	case "$arch" in \
		'x86_64') \
			url='https://dl.google.com/go/go1.25.4.linux-amd64.tar.gz'; \
			sha256='9fa5ffeda4170de60f67f3aa0f824e426421ba724c21e133c1e35d6159ca1bec'; \
			;; \
		'aarch64') \
			url='https://dl.google.com/go/go1.25.4.linux-arm64.tar.gz'; \
			sha256='a68e86d4b72c2c2fecf7dfed667680b6c2a071221bbdb6913cf83ce3f80d9ff0'; \
			;; \
		*) echo >&2 "error: unsupported architecture '$arch' (likely packaging update needed)"; exit 1 ;; \
	esac; \
	\
	wget -O go.tgz "$url"; \
	echo "$sha256 *go.tgz" | sha256sum -c -; \
	tar -C /usr/local -xzf go.tgz; \
	rm go.tgz; \
	\
# Clean up to reduce image size
	microdnf remove -y tar wget; \
	microdnf clean all; \
	rm -rf /var/cache/microdnf/*; \
	\
# Smoke test
	go version

RUN mkdir -p "$GOPATH/src" "$GOPATH/bin" && chmod -R 1777 "$GOPATH"
WORKDIR $GOPATH