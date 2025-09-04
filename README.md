# build opencv wheels

## install uv

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

## screen/tmux

```
sudo apt install screen tmux
```

```
tmux new-session -s opencv_build
tmux attach-session -t opencv_build
tmux list-sessions
```

## System Deps
```
sudo apt install screen git

# build deps
sudo apt update && sudo apt install -y cmake g++ wget unzip libopenblas-dev


# shared libraries
sudo apt install \
    libturbojpeg0-dev \
    libpng-dev \
    libtiff-dev \
    libwebp-dev \
    libopenjp2-7-dev \
    zlib1g-dev \
    libavcodec-dev \
    libavformat-dev
```

Start named session
```
screen -S opencv_build
```

## setup locations
```
mdkir /media/ssd
sudo mount -t ext4 /dev/sda3 /media/ssd
mkdir /media/ssd/repos
sudo chown -R $USER:$USER /media/ssd
```

## Swapfile configuration
```bash
export SWAPFILE=/media/ssd/swapfile
sudo fallocate -l 4G $SWAPFILE
sudo chmod 600 $SWAPFILE
sudo mkswap $SWAPFILE
sudo swapon $SWAPFILE
```

```
git clone --recursive --depth=1 https://github.com/opencv/opencv-python.git
cd opencv-python
git fetch --tags

# checkout tagged version of opencv-python
git checkout 88

## checkout versions as needed
cd opencv

# checkout tagged version of opencv
git checkout 4.12.0
cd ..

uv python pin 3.11
uv venv
source .venv/bin/activate

export UV_DEFAULT_INDEX=https://piwheels.org/simple
export UV_EXTRA_INDEX_URL=https://pypi.org/simple

export UV_CACHE_DIR=/media/ssd/uv-cache
mkdir $UV_CACHE_DIR

uv pip install "numpy==2.0.2" packaging "scikit-build>=0.14.0" "setuptools==59.2.0"

uv build -v --wheel .
```





## Other ways?

```
# github actions does this
source scripts/build.sh
```

## Resources

