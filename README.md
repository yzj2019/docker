
## 简介
该项目用于自动构建和推送 CUDA 相关的 Docker 镜像到 Docker Hub。

- 配置文件 `docker_build_config.json`
  定义要构建的镜像的 CUDA 版本、CUDNN 版本和 Ubuntu 版本
  ```json
  {
  "configurations": [
    {
      "name": "cuda11-ubuntu20",  # 名称，没什么用
      "cuda": "11.6.1",             # CUDA 版本
      "cudnn": "8",               # CUDNN 版本
      "ubuntu": "20.04",          # Ubuntu 版本
      "enabled": true             # 是否启用
    }
  ]
  }
  ```
  具体版本选择参考 [NVIDIA Docker](https://hub.docker.com/r/nvidia/cuda/tags?page=1)
- 最终得到的镜像 tag 格式类似 `${DOCKERHUB_USERNAME}/cuda:11.6.1_cudnn8_ubuntu20.04`

## 用户配置
在 github action 里配置了自动构建和推送镜像，需要先做如下配置

- 创建 Docker Hub 访问令牌
  登录 Docker Hub 账号，点击 `头像 -> Account Settings -> Personal access tokens -> New Access Token`
- 配置 GitHub Secrets
  在 GitHub 仓库中，点击 `Settings -> Secrets and variables -> Actions -> New repository secret`，添加以下 secrets：
  - `DOCKERHUB_USERNAME`: 你的 Docker Hub 用户名
  - `DOCKERHUB_TOKEN`: 之前创建的 Docker Hub 访问令牌

## 自动构建和推送镜像
可以在 commit 后添加标签并推送到 github，触发自动构建和推送镜像到 dockerhub
```bash
# 创建标签
git tag v1.0.0
# 推送标签到远程仓库
git push origin v1.0.0
```

## 手动构建本地镜像
```bash
nohup sh docker_build.sh {DOCKER_USER_NAME} {CUDA_VERSION} {CUDNN_VERSION} {UBUNTU_VERSION} > {LOG_PATH} 2>&1 &
# e.g.
# nohup sh docker_build.sh yuzijian 11.6.1 8 20.04 > ./logs/docker_build_1.log 2>&1 &
```

## 运行容器
```bash
sh docker_run.sh
```

## TODO
- [ ] 添加连接用的 ssh key 到 github action secrets
- [ ] 验证 docker 桌面、nomachine 连接是否配置成功
- [ ] 考虑是否要删除 zerotier
- [ ] 考虑添加显卡驱动，详见 GeForce-XorgDisplaySettingAuto.sh
- [ ] 考虑更换为 ubuntu desktop