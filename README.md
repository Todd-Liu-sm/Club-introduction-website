# 项目文档 — 中国海洋大学社团展示网站

版本：1.0

日期：2026-05-30

状态：规划 / 可交付原型

## 目录

- 概要
- 目标与范围
- 交付物
- 功能清单
- 用户角色与权限
- 技术架构
- 数据与安全
- 部署与运维
- 里程碑与时间表
- 验收标准
- 开发与部署指南（简要）
- 示例 Nginx 配置
- 数据库表结构（示例 SQL）
- 快速部署示例（Systemd 服务）
- 项目结构

## 项目结构（仓库示例）

- deploy/ — 部署相关配置与脚本（`nginx.conf`, `schema.sql`, `club-backend.service`）
- frontend/ — 前端源码（Vue 3 + Vite）
- backend/ — 后端源码（Spring Boot）
- README.md — 本项目文档

## 概要
本项目面向中国海洋大学在校师生，提供一套轻量化的社团展示与信息维护平台。系统聚焦社团信息公开展示与联系方式获取，目标在最短周期内上线可用的校内展示服务，为后续招录、报名等功能扩展提供基础数据与接口支持。

## 目标与范围
- 目标：搭建校内可访问的社团信息展示平台，实现社团信息统一展示、关键词检索、联系方式脱敏展示与基础的社团管理功能。
- 范围边界：本版本不包含报名、筛选、支付或复杂的用户画像功能；仅限校园内访问，采用校园邮箱进行身份认证。

## 交付物
- 可部署的前端静态资源（Vue 3 + Vite 项目）
- 后端服务（Spring Boot + MySQL）及数据库初始化脚本
- Nginx 配置示例（含校园网访问控制方案）
- 部署与运维说明文档
- 简易管理员后台用于信息审核与社团管理

## 功能清单
- 学生端
	- 社团分类浏览与关键词检索
	- 社团详情页（简介、代表活动、脱敏联系方式）
	- 收藏功能与个人中心（基础）
- 社团管理端
	- 社团信息编辑（名称、简介、封面、联系方式）
	- 提交审核流程（提交 → 管理员审核 → 上线）
- 平台管理端
	- 社团入驻与资质审核
	- 内容抽查与违规处理
	- 基础统计（访问量、浏览量）

## 用户角色与权限
- 未登录用户：可浏览社团列表与卡片（名称、封面图）
- 登录学生：可查看完整社团信息与脱敏联系方式、收藏社团
- 社团管理员：可编辑所属社团信息并提交审核
- 平台管理员：审核社团、发布/下线内容、管理用户权限

## 技术架构
- 前端：Vue 3 + Vite + Element Plus
- 后端：Spring Boot（Java）
- 数据库：MySQL 8
- 部署：Linux + Nginx（反向代理、静态资源、IP 白名单）

### 关键实现点
- 校内访问控制：Nginx 根据校园网 IP 段或内网入口做白名单校验
- 校园邮箱登录：后端接入校园认证或 SSO，仅允许 edu 邮箱登录
- 联系方式脱敏：前端与后端协同，展示前进行掩码处理并记录访问日志

## 数据与安全
- 仅收集必要字段：社团名称、简介、分类、代表图片、联系方式等
- 隐私保护：联系方式展示掩码，后台仅向管理员展示全部信息
- 访问控制：管理接口强制管理员权限，关键操作有审计日志
- 备份与恢复：建议定期导出数据库快照并存储校园内备份设备

## 部署与运维
- 最低资源建议：1 vCPU、1–2 GB RAM（小型 VM）
- 推荐架构：前端静态资源由 Nginx 承载，后端部署在同一或分离的应用主机，上游使用数据库实例
- 运维任务：证书管理、IP 白名单维护、定期备份、日志轮转

## 里程碑与时间表（示例）
- M1（2 周）：需求确认、原型与界面设计
- M2（4 周）：前端基础页面与后端基础 API 实现
- M3（2 周）：社团管理后台与审核流程、测试
- M4（1 周）：部署与内部验收、修复问题

## 验收标准
- 学生端能按分类与关键词正确检索并查看社团详情
- 社团管理员能提交编辑并通过管理员审核上线
- 系统在校园网环境中可访问并且访问控制生效

## 贡献与联系方式
欢迎校内开发者与管理方参与改进。请通过仓库 Issues 提交建议或联系项目负责人以对接校园认证资源。

## 许可证
本项目采用 MIT 许可证。

## 开发与部署指南（简要）

1. 本地开发
	 - 前端：进入 `frontend/`，安装依赖并运行开发服务器
		 ```bash
		 cd frontend
		 npm install
		 npm run dev
		 ```
	 - 后端：进入 `backend/`，使用 Maven/Gradle 运行（示例 Maven）
		 ```bash
		 cd backend
		 mvn spring-boot:run
		 ```
	 - 数据库：在本地或开发库中创建数据库并导入示例 schema（见下方 SQL）。

2. 构建与部署（生产）
	 - 构建前端静态文件：
		 ```bash
		 cd frontend
		 npm run build
		 ```
	 - 打包后端（示例 Maven）：
		 ```bash
		 cd backend
		 mvn clean package -DskipTests
		 java -jar target/backend.jar
		 ```
	 - 将前端 `dist/` 部署到 Nginx 静态目录，后端部署为系统服务或容器。

3. 环境变量（示例）
	 - `SPRING_DATASOURCE_URL`，`SPRING_DATASOURCE_USERNAME`，`SPRING_DATASOURCE_PASSWORD`
	 - `JWT_SECRET`（若使用 JWT）、`ADMIN_EMAILS`（逗号分隔）

4. 备份与恢复
	 - 建议每天导出 MySQL 备份：
		 ```bash
		 mysqldump -u root -p --single-transaction club_db > club_db_$(date +%F).sql
		 ```

## 示例 Nginx 配置

下面为一个最小示例，前端静态文件由 Nginx 提供，API 请求代理到本地后端，示例同时展示了基于 IP 的白名单策略（请替换为校园网真实网段）。

```nginx
server {
	listen 80;
	server_name club.example.edu;

	# 仅允许校园网 IP 段访问（示例，请替换为实际网段）
	# 可添加多条 allow
	allow 10.0.0.0/8;
	deny all;

	root /var/www/club-frontend/dist;
	index index.html;

	location /api/ {
		proxy_pass http://127.0.0.1:8080/;
		proxy_set_header Host $host;
		proxy_set_header X-Real-IP $remote_addr;
		proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
		proxy_http_version 1.1;
		proxy_set_header Connection "";
	}

	location / {
		try_files $uri $uri/ /index.html;
	}

	access_log /var/log/nginx/club_access.log;
	error_log /var/log/nginx/club_error.log;
}
```

## 数据库表结构（示例 SQL）

下面给出用于实现核心功能的示例表结构（MySQL 语法，供参考）。建议把本节内容另存为 `deploy/schema.sql` 并通过 CI/部署流程导入。

-- 用户表
```sql
CREATE TABLE users (
	id BIGINT AUTO_INCREMENT PRIMARY KEY,
	email VARCHAR(255) NOT NULL UNIQUE,
	display_name VARCHAR(100),
	role ENUM('student','club_admin','admin') NOT NULL DEFAULT 'student',
	created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

-- 社团表
```sql
CREATE TABLE clubs (
	id BIGINT AUTO_INCREMENT PRIMARY KEY,
	name VARCHAR(200) NOT NULL,
	category VARCHAR(50),
	summary VARCHAR(512),
	description TEXT,
	cover_image VARCHAR(512),
	status ENUM('pending','published','rejected') NOT NULL DEFAULT 'pending',
	created_by BIGINT,
	created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
	FOREIGN KEY (created_by) REFERENCES users(id)
);
```

-- 联系方式表
```sql
CREATE TABLE contacts (
	id BIGINT AUTO_INCREMENT PRIMARY KEY,
	club_id BIGINT NOT NULL,
	type VARCHAR(50),
	value VARCHAR(255),
	is_public BOOLEAN DEFAULT TRUE,
	created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
	FOREIGN KEY (club_id) REFERENCES clubs(id) ON DELETE CASCADE
);
```

-- 收藏表
```sql
CREATE TABLE favorites (
	id BIGINT AUTO_INCREMENT PRIMARY KEY,
	user_id BIGINT NOT NULL,
	club_id BIGINT NOT NULL,
	created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
	UNIQUE KEY ux_user_club (user_id, club_id),
	FOREIGN KEY (user_id) REFERENCES users(id),
	FOREIGN KEY (club_id) REFERENCES clubs(id)
);
```

-- 审计日志
```sql
CREATE TABLE audit_logs (
	id BIGINT AUTO_INCREMENT PRIMARY KEY,
	actor_id BIGINT,
	action VARCHAR(100),
	entity VARCHAR(50),
	entity_id BIGINT,
	detail JSON,
	created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

## 快速部署示例（Systemd 服务）

示例：为后端创建 systemd 服务文件 `/etc/systemd/system/club-backend.service`（示例，请根据部署位置调整路径）：

```ini
[Unit]
Description=Club Backend
After=network.target

[Service]
User=www-data
WorkingDirectory=/opt/club/backend
ExecStart=/usr/bin/java -jar /opt/club/backend/backend.jar
SuccessExitStatus=143
Restart=on-failure
RestartSec=10

[Install]
WantedBy=multi-user.target
```

部署后启用服务：

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now club-backend
```

---


