FROM golang:1.21-alpine AS builder
WORKDIR /code
ENV CGO_ENABLED=0
RUN go mod init backend
COPY main.go .
RUN go mod tidy
RUN go build -o backend main.go

# FROM alpine:3.23.2
FROM scratch
WORKDIR /app
COPY --from=builder /code/backend .
ENTRYPOINT ["/app/backend"]
