FROM ubuntu:16.04

RUN apt-get update && apt-get install -y curl && \
    curl -sL https://deb.nodesource.com/setup_10.x | bash && \
    apt-get install -y nodejs && \ 
    apt-get purge -y --auto-remove curl && \ 
    rm -rf /var/lib/apt/lists/* && \
    useradd -m user

WORKDIR /backend
COPY . .
RUN npm install && chown -hR user /backend
USER user

CMD ["npm", "start"]

EXPOSE 8000