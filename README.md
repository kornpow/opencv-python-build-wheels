# build opencv wheels

```
git clone --recursive --depth=1 https://github.com/opencv/opencv-python.git
cd opencv-python

# checkout versions as needed

source scripts/build.sh

uv build -v --wheel -o out/git
```

