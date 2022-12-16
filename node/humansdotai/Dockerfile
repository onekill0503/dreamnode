FROM ubuntu:20.04
WORKDIR /root/
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
    && go build -o /root/go/bin/humansd cmd/humansd/main.go

# configuration mises
ENV HUMAN_CHAIN_ID="[chain]"
ENV NODENAME="[nodename]"
ENV WALLET="[wallet]"

RUN humansd config chain-id $HUMAN_CHAIN_ID \
    && humansd config keyring-backend test \
    && humansd init $NODENAME --chain-id $HUMAN_CHAIN_ID \
    && curl https://rpc-testnet.humans.zone/genesis | jq .result.genesis > /root/.humans/config/genesis.json \
    && sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"[peers]\"/" /root/.humans/config/config.toml

# set api on
RUN sed -i "117 s/false/true/" /root/.humans/config/app.toml \
    && sed -i "120 s/false/true/" /root/.humans/config/app.toml \
    && sed -i -e "s/^enabled-unsafe-cors *=.*/enabled-unsafe-cors = true/" /root/.humans/config/app.toml

# set block time parameter
RUN sed -i -e "s/^timeout_propose *=.*/timeout_propose = \"100ms\"/" /root/.humans/config/app.toml \
    && sed -i -e "s/^timeout_propose_delta  *=.*/timeout_propose_delta  = \"500ms\"/" /root/.humans/config/app.toml \
    && sed -i -e "s/^timeout_prevote *=.*/timeout_prevote = \"100ms\"/" /root/.humans/config/app.toml \
    && sed -i -e "s/^timeout_prevote_delta *=.*/timeout_prevote_delta = \"500ms\"/" /root/.humans/config/app.toml \
    && sed -i -e "s/^timeout_precommit *=.*/timeout_precommit = \"100ms\"/" /root/.humans/config/app.toml \
    && sed -i -e "s/^timeout_precommit_delta  *=.*/timeout_precommit_delta  = \"500ms\"/" /root/.humans/config/app.toml \
    && sed -i -e "s/^timeout_commit  *=.*/timeout_commit  = \"1s\"/" /root/.humans/config/app.toml \
    && sed -i -e "s/^skip_timeout_commit  *=.*/skip_timeout_commit  = \"false\"/" /root/.humans/config/app.toml 

# set custom config
RUN sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" /root/.humans/config/app.toml \
    && sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" /root/.humans/config/app.toml \
    && sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"0\"/" /root/.humans/config/app.toml \
    && sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"10\"/" /root/.humans/config/app.toml \
    && sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.025uheart\"/" /root/.humans/config/app.toml \
    && sed -i -e "s/prometheus = false/prometheus = true/" /root/.humans/config/config.toml

# set stateSync if on
[stateSync]

# EXPOSING rpc Port
EXPOSE 26657
# EXPOSING api port
EXPOSE 1317

# run fullnode
ENTRYPOINT ["humansd" , "start", "--home" , "/root/.humans"]