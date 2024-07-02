# RTMP-Web

一个简易的使用`flv.js`搭建的 flv 解析网页。

## Usage

```bash
# clone
git clone https://github.com/JulienZeng/RTMP-Web.git

# install
cd RTMP-Web
# use what you like
pnpm i
# or npm i
# or yarn i
# ...

# dev
cp ./src/config/config-example.yaml ./src/config/config.yaml
pnpm run dev

# build
cp ./src/config/config-example.yaml ./src/config/config.yaml # if you have this file, ignore this command.
pnpm run build
```

- 使用前请将`src/config/config.yaml`是可配置的，为了方便你的使用，如有需要请前往修改。

## 技术栈

- Vue3
- TypeScript
- Element Plus
