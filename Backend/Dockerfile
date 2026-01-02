FROM rust:1.70 as builder

RUN apt-get update && apt-get install -y libpq-dev
RUN cargo install diesel_cli --version 2.1.0 --no-default-features --features postgres --locked

WORKDIR /app
COPY . .
RUN cargo build --release

FROM debian:bullseye-slim

RUN apt-get update && apt-get install -y libpq-dev && rm -rf /var/lib/apt/lists/*

COPY --from=builder /app/target/release/login-app-backend ./login-app-backend
COPY --from=builder /usr/local/cargo/bin/diesel /usr/local/bin/diesel
COPY --from=builder /app/migrations ./migrations

EXPOSE 8080
CMD ["sh", "-c", "/usr/local/bin/diesel migration run && ./login-app-backend"]