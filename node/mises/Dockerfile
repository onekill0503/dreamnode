FROM ubuntu:20.04
WORKDIR $HOME/
# Update system
RUN apt update\
    && apt upgrade -y
# Instal depedencies package
RUN  apt install curl build-essential git wget jq make gcc tmux chrony -y
# installing golang version 1.19
RUN wget "https://golang.org/dl/go1.19.linux-amd64.tar.gz"
RUN tar -C /usr/local -xzf "./go1.19.linux-amd64.tar.gz" 
RUN rm "./go1.19.linux-amd64.tar.gz"
ENV PATH=/usr/local/go/bin:$HOME/go/bin
# installing mises binary
RUN git clone https://github.com/mises-id/mises-tm/ \
    && cd ./mises-tm \
    && git checkout 1.0.4 \
    && ls -la \
    && make build . \
    && make install .
# configuration mises
ENV MISES_CHAIN_ID=mainnet
ENV NODENAME=alwaysbedream2
ENV WALLET=wallet

RUN misestmd config chain-id $MISES_CHAIN_ID \
    && misestmd config keyring-backend test \
    && misestmd init $NODENAME --chain-id $MISES_CHAIN_ID \
    && curl https://e1.mises.site:443/genesis | jq .result.genesis > ~/.misestm/config/genesis.json \
    && sed -i.bak -e 's/^persistent_peers *=.*/persistent_peers = "40889503320199c676570b417b132755d0414332@rpc.gw.mises.site:26656"' $HOME/.misestm/config/config.toml
# set custom config
RUN sed -i -e 's/^pruning *=.*/pruning = "custom"' $HOME/.misestm/config/app.toml \
    && sed -i -e 's/^pruning-keep-recent *=.*/pruning-keep-recent = "100"' $HOME/.misestm/config/app.toml \
    && sed -i -e 's/^pruning-keep-every *=.*/pruning-keep-every = "0"' $HOME/.misestm/config/app.toml \
    && sed -i -e 's/^pruning-interval *=.*/pruning-interval = "50"' $HOME/.misestm/config/app.toml \
    && sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.000025umis\"/" $HOME/.misestm/config/app.toml \
    && sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.misestm/config/config.toml

# run fullnode
CMD ["misestmd" , "start", "--home $HOME/.misestm"]