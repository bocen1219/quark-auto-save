# 部署指南

> Docker 镜像标签：本地测试用 **test**，远程正式版用 **latest**

## 本地构建 & 推送镜像

### 构建

```bash
# 构建测试镜像
docker build -t boboji257/quark-auto-save:test .
```

### 本地测试运行

```bash
# 用 test 标签启动测试容器
cd E:/quark-auto-save; docker build -t boboji257/quark-auto-save:test .; docker rm -f quark-auto-save; docker run -d --name quark-auto-save -p 5005:5005 -v "E:/quark-auto-save/quark-auto-save/config:/app/config" -v "E:/quark-auto-save/quark-auto-save/media:/app/media" -e TZ=Asia/Shanghai boboji257/quark-auto-save:test

# 查看日志
docker logs -f qas-test

# 测试没问题后停止
docker stop qas-test && docker rm qas-test
```

### 推送

```bash
# 测试通过后，打上 latest 标签并推送
docker tag boboji257/quark-auto-save:test boboji257/quark-auto-save:latest
docker push boboji257/quark-auto-save:latest
```

### 构建 + 推送（一键）

```bash
docker build -t boboji257/quark-auto-save:test . ; docker tag boboji257/quark-auto-save:test boboji257/quark-auto-save:latest ; docker push boboji257/quark-auto-save:latest
```

## NAS 部署 & 更新

SSH 或群晖任务计划执行。

### 首次部署

```bash
cd /volume1/docker/你的compose目录

docker compose pull quark-auto-save
docker compose up -d quark-auto-save
```

### 更新单个容器（推荐，不影响其他服务）

```bash
cd /volume1/docker/你的compose目录

docker compose pull quark-auto-save
docker compose up -d --force-recreate quark-auto-save
```

### 更新所有容器

```bash
cd /volume1/docker/你的compose目录

docker compose pull
docker compose up -d
```

### 排查问题

```bash
docker compose ps
docker compose logs -f quark-auto-save
docker compose logs --tail=100 quark-auto-save
```

### 旧容器冲突处理

```bash
docker stop 旧容器名
docker rm 旧容器名
docker compose up -d
```

## 群晖任务计划（无需 SSH）

1. 控制面板 → 任务计划 → 新增 → 计划的任务 → 用户自定义脚本
2. 运行账号选 **root**
3. 脚本内容：

   ```bash
   cd /volume1/docker/你的compose目录
   docker compose pull quark-auto-save
   docker compose up -d quark-auto-save
   ```

4. 保存后手动运行一次
