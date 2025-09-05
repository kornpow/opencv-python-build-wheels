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

# To leave tmux session when inside press:
Ctrl+b, then d
```

## System Deps
```
sudo apt install screen git

# build deps
sudo apt update && sudo apt install -y cmake g++ wget unzip libopenblas-dev python3-dev


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

uv pip install "numpy==2.0.2" packaging "scikit-build>=0.14.0" "setuptools==59.2.0" pip

uv build -v --wheel .
```


## Modify CMake Flags

`nano setup.py`
```
    cmake_args = (
        (ci_cmake_generator if is_CI_build else [])
        + [
            # skbuild inserts PYTHON_* vars. That doesn't satisfy opencv build scripts in case of Py3
            "-DPYTHON3_EXECUTABLE=%s" % sys.executable,
            "-DPYTHON_DEFAULT_EXECUTABLE=%s" % sys.executable,
            "-DPYTHON3_INCLUDE_DIR=%s" % python_include_dir,
            "-DPYTHON3_LIBRARY=%s" % python_lib_path,
            "-DBUILD_opencv_python3=ON",
            "-DBUILD_opencv_python2=OFF",
            # Disable the Java build by default as it is not needed
            "-DBUILD_opencv_java=%s" % build_java,
            # Relative dir to install the built module to in the build tree.
            # The default is generated from sysconfig, we'd rather have a constant for simplicity
            "-DOPENCV_PYTHON3_INSTALL_PATH=python",
            # Otherwise, opencv scripts would want to install `.pyd' right into site-packages,
            # and skbuild bails out on seeing that
            "-DINSTALL_CREATE_DISTRIB=ON",
            # See opencv/CMakeLists.txt for options and defaults
            "-DBUILD_opencv_apps=OFF",
            "-DBUILD_opencv_freetype=OFF",
            "-DBUILD_SHARED_LIBS=OFF",
            "-DBUILD_JPEG=OFF",
            "-DBUILD_PNG=OFF",
            "-DBUILD_TIFF=OFF",
            "-DBUILD_WEBP=OFF",
            "-DBUILD_OPENJPEG=OFF",
            "-DBUILD_ZLIB=OFF",
            "-DBUILD_TESTS=OFF",
            "-DBUILD_PERF_TESTS=OFF",
            "-DBUILD_DOCS=OFF",
            "-DPYTHON3_LIMITED_API=ON",
            "-DBUILD_OPENEXR=OFF",
        ]
```





## Other ways?

```
# github actions does this
source scripts/build.sh
```

## Resources

