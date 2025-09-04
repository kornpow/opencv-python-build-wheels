# build opencv wheels

```
git clone --recursive --depth=1 https://github.com/opencv/opencv-python.git
cd opencv-python

## checkout versions as needed
cd opencv
git checkout 4.12.0

uv python pin 3.11
uv venv
source .venv/bin/activate

export UV_DEFAULT_INDEX=https://piwheels.org/simple
export UV_EXTRA_INDEX_URL=https://pypi.org/simple

uv pip install "numpy==2.0.2" packaging "scikit-build>=0.14.0" "setuptools==59.2.0"

uv build -v --wheel -o out/git
```



## Swapfile configuration
```bash
sudo fallocate -l 2G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
```

## Other ways?

```
# github actions does this
source scripts/build.sh
```

## Resources

