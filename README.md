# heroku-pipesock-buildpack

The implementation of pipesock in full

```
#!/bin/sh
BUILD_DIR=$1
CACHE_DIR=$2
ENV_DIR=$3
INSTALL_DIR=$BUILD_DIR/vendor/pipesocks/
ETC=$INSTALL_DIR/etc
INIT_SCRIPT="$BUILD_DIR/.profile.d/pipesocksinit.sh"
#  DOWNLOAD_URL="http://www.xunsearch.com/scws/down/scws-1.2.2.tar.bz2"
echo "============================================"
echo "Build pipesocks module"
echo "============================================"
echo "clone and git get into ${INSTALL_DIR}"
echo "============================================"
sudo apt-get -y install git make build-essential qt5-default qt5-qmake libtool autoconf automake
mkdir -p $INSTALL_DIR
git clone https://github.com/pipesocks/pipesocks.git
cd pipesocks/
git checkout stable
git submodule update --init --recursive
cd libsodium/
./autogen.sh
./configure
make && sudo make install
sudo cp /usr/local/lib/libsodium.so.18 /usr/lib/
cd ../pipesocks/
qmake server.pipesocks.pro && make
sudo cp pipesocks /usr/bin/

##tar -zxvf dict.tar.gz -C $ETC
echo "Building runtime environment for pipesocks installation"
mkdir -p $BUILD_DIR/.profile.d
##echo "export PATH=\"$INSTALL_DIR/bin:\$PATH\"" > $INIT_SCRIPT
##echo "export SCWSDICT=\"$INSTALL_DIR/etc:\$PATH\"" > $INIT_SCRIPT
echo "./pipesocks pump -p $ENV_DIR/PORT -k $ENV_DIR/PASS" > $INIT_SCRIPT

```
