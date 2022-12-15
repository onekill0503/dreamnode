FROM ubuntu:20.04
WORKDIR ~/
# Update system
RUN apt-get update\
    && apt-get upgrade -y
# Instal depedencies package
RUN  apt-get install curl build-essential git wget jq make gcc tmux chrony -y
# installing golang version 1.19
RUN wget "https://golang.org/dl/go1.19.linux-amd64.tar.gz"
RUN tar -C /usr/local -xzf "./go1.19.linux-amd64.tar.gz" 
RUN rm "./go1.19.linux-amd64.tar.gz"
ENV PATH="$PATH:/usr/local/go/bin:/root/go/bin"
# installing mises binary
RUN git clone https://github.com/humansdotai/humans \
    && cd ./humans \
    && git checkout v1.0.0 \
    && make build \
    && make install
# configuration mises
ENV HUMAN_CHAIN_ID="[chain]"
ENV NODENAME="[nodename]"
ENV WALLET="[wallet]"

RUN humansd config chain-id $HUMAN_CHAIN_ID \
    && humansd config keyring-backend test \
    && humansd init $NODENAME --chain-id $HUMAN_CHAIN_ID \
    && curl https://rpc-testnet.humans.zone/genesis | jq .result.genesis > ~/.humans/config/genesis.json \
    && sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"1df6735ac39c8f07ae5db31923a0d38ec6d1372b@45.136.40.6:26656,9726b7ba17ee87006055a9b7a45293bfd7b7f0fc@45.136.40.16:26656,6e84cde074d4af8a9df59d125db3bf8d6722a787@45.136.40.18:26656,eda3e2255f3c88f97673d61d6f37b243de34e9d9@45.136.40.13:26656,4de8c8acccecc8e0bed4a218c2ef235ab68b5cf2@45.136.40.12:26656\"/" ~/.humans/config/config.toml
# set block time parameter
RUN sed -i -e "s/^timeout_propose *=.*/timeout_propose = \"100ms\"/" ~/.humans/config/app.toml \
    && sed -i -e "s/^timeout_propose_delta  *=.*/timeout_propose_delta  = \"500ms\"/" ~/.humans/config/app.toml \
    && sed -i -e "s/^timeout_prevote *=.*/timeout_prevote = \"100ms\"/" ~/.humans/config/app.toml \
    && sed -i -e "s/^timeout_prevote_delta *=.*/timeout_prevote_delta = \"500ms\"/" ~/.humans/config/app.toml \
    && sed -i -e "s/^timeout_precommit *=.*/timeout_precommit = \"100ms\"/" ~/.humans/config/app.toml \
    && sed -i -e "s/^timeout_precommit_delta  *=.*/timeout_precommit_delta  = \"500ms\"/" ~/.humans/config/app.toml \
    && sed -i -e "s/^timeout_commit  *=.*/timeout_commit  = \"1s\"/" ~/.humans/config/app.toml \
    && sed -i -e "s/^skip_timeout_commit  *=.*/skip_timeout_commit  = \"false\"/" ~/.humans/config/app.toml 

# set custom config
RUN sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" ~/.humans/config/app.toml \
    && sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" ~/.humans/config/app.toml \
    && sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"0\"/" ~/.humans/config/app.toml \
    && sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"10\"/" ~/.humans/config/app.toml \
    && sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.025uheart\"/" ~/.humans/config/app.toml \
    && sed -i -e "s/prometheus = false/prometheus = true/" ~/.humans/config/config.toml

# run fullnode
ENTRYPOINT ["humansd" , "start", "--home" , "/root/.humans"]