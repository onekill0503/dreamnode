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
RUN git clone https://github.com/mises-id/mises-tm/ \
    && cd ./mises-tm \
    && git checkout 1.0.4 \
    && make build \
    && make install
# configuration mises
ENV MISES_CHAIN_ID="[chain]"
ENV NODENAME="[nodename]"
ENV WALLET="[wallet]"

# set monikor based on nodename
RUN sed -i -e "s/^moniker *=.*/moniker = \"$NODENAME\"/" ~/.humans/config/config.toml

# set api on
RUN sed -i -e "s/^enable *=.*/enable = true/" ~/.humans/config/config.toml \
    && sed -i -e "s/^enabled-unsafe-cors *=.*/enabled-unsafe-cors = true/" ~/.humans/config/app.toml

RUN misestmd config chain-id $MISES_CHAIN_ID \
    && misestmd config keyring-backend test \
    && misestmd init $NODENAME --chain-id $MISES_CHAIN_ID \
    && curl https://e1.mises.site:443/genesis | jq .result.genesis > ~/.misestm/config/genesis.json \
    && sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"[peers]\"/" ~/.misestm/config/config.toml
# set custom config
RUN sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" ~/.misestm/config/app.toml \
    && sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" ~/.misestm/config/app.toml \
    && sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"0\"/" ~/.misestm/config/app.toml \
    && sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"50\"/" ~/.misestm/config/app.toml \
    && sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.000025umis\"/" ~/.misestm/config/app.toml \
    && sed -i -e "s/prometheus = false/prometheus = true/" ~/.misestm/config/config.toml
    
# EXPOSING rpc Port
EXPOSE 26657
# EXPOSING api port
EXPOSE 1317

# run fullnode
ENTRYPOINT ["misestmd" , "start", "--home" , "/root/.misestm"]