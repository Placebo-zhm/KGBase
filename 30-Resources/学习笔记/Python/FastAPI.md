---
type: resource
status: draft
created: 2026-05-19
updated: 2026-05-19
tags:
  - Python
  - FastAPI
  - 后端
  - API
---

# FastAPI 学习笔记

## 来源

- 官方文档：<https://fastapi.tiangolo.com/>
- 第一个应用：<https://fastapi.tiangolo.com/tutorial/first-steps/>
- 请求体：<https://fastapi.tiangolo.com/tutorial/body/>
- 依赖注入：<https://fastapi.tiangolo.com/tutorial/dependencies/>
- 多文件项目结构：<https://fastapi.tiangolo.com/tutorial/bigger-applications/>
- 测试：<https://fastapi.tiangolo.com/tutorial/testing/>

## 摘要

FastAPI 是一个基于 Python 类型注解构建 Web API 的框架，底层依赖 Starlette 处理 Web 能力，依赖 Pydantic 处理数据校验、序列化和文档 Schema。它适合用来快速构建 REST API、机器学习服务接口、后台管理接口和微服务。

核心理解：

- 路由函数的参数类型注解就是接口契约。
- Pydantic 模型负责请求体和响应体的数据结构。
- `Depends` 用来复用鉴权、数据库会话、分页参数等公共逻辑。
- 自动生成 `/docs` 和 `/redoc` 文档，适合开发调试和接口协作。

## 安装

推荐在虚拟环境中安装：

```bash
pip install "fastapi[standard]"
```

如果只安装核心依赖，也可以：

```bash
pip install fastapi uvicorn
```

常用运行命令：

```bash
uvicorn main:app --reload
```

含义：

- `main`：`main.py` 文件。
- `app`：文件中的 `FastAPI()` 实例变量。
- `--reload`：开发模式，代码变更后自动重启。

## 最小示例

```python
from fastapi import FastAPI

app = FastAPI()


@app.get("/")
def read_root():
    return {"message": "Hello, FastAPI!"}


@app.get("/items/{item_id}")
def read_item(item_id: int, q: str | None = None):
    return {"item_id": item_id, "q": q}
```

启动后访问：

- `http://127.0.0.1:8000/`
- `http://127.0.0.1:8000/items/1?q=test`
- `http://127.0.0.1:8000/docs`
- `http://127.0.0.1:8000/redoc`

## 路由与请求参数

### 路径参数

路径参数来自 URL 路径的一部分：

```python
@app.get("/users/{user_id}")
def get_user(user_id: int):
    return {"user_id": user_id}
```

`user_id: int` 会自动完成：

- 类型转换。
- 参数校验。
- OpenAPI 文档生成。
- 校验失败时返回 422。

### 查询参数

不在路径中声明，但出现在函数参数里的变量，会被当作查询参数：

```python
@app.get("/users")
def list_users(page: int = 1, size: int = 20, keyword: str | None = None):
    return {"page": page, "size": size, "keyword": keyword}
```

请求示例：

```text
GET /users?page=1&size=10&keyword=tom
```

### 请求头、Cookie 和状态码

```python
from fastapi import Cookie, Header, status


@app.get("/profile", status_code=status.HTTP_200_OK)
def profile(
    user_agent: str | None = Header(default=None),
    session_id: str | None = Cookie(default=None),
):
    return {"user_agent": user_agent, "session_id": session_id}
```

## 请求体与 Pydantic 模型

请求体通常用 Pydantic 模型声明：

```python
from pydantic import BaseModel, Field


class ItemCreate(BaseModel):
    name: str = Field(min_length=1, max_length=50)
    price: float = Field(gt=0)
    description: str | None = None


@app.post("/items")
def create_item(item: ItemCreate):
    return item
```

重点：

- `BaseModel` 描述 JSON 请求体结构。
- `Field` 用于字段级校验和文档说明。
- 类型不匹配或缺少必填字段时，FastAPI 会自动返回 422。

## 响应模型

响应模型用于控制返回结构，避免把不该暴露的字段返回给前端。

```python
from pydantic import BaseModel


class UserInDB(BaseModel):
    id: int
    username: str
    hashed_password: str


class UserOut(BaseModel):
    id: int
    username: str


@app.get("/users/{user_id}", response_model=UserOut)
def get_user(user_id: int):
    return UserInDB(id=user_id, username="alice", hashed_password="secret")
```

这里实际返回值里有 `hashed_password`，但接口响应会按 `UserOut` 过滤。

## 常用 HTTP 方法

```python
@app.get("/items")
def list_items():
    return []


@app.post("/items")
def create_item():
    return {"ok": True}


@app.put("/items/{item_id}")
def replace_item(item_id: int):
    return {"item_id": item_id}


@app.patch("/items/{item_id}")
def update_item(item_id: int):
    return {"item_id": item_id}


@app.delete("/items/{item_id}")
def delete_item(item_id: int):
    return {"deleted": item_id}
```

常见约定：

- `GET`：查询资源。
- `POST`：创建资源或提交动作。
- `PUT`：整体替换资源。
- `PATCH`：局部更新资源。
- `DELETE`：删除资源。

## 异常处理

```python
from fastapi import HTTPException


@app.get("/items/{item_id}")
def get_item(item_id: int):
    if item_id <= 0:
        raise HTTPException(status_code=400, detail="item_id must be positive")
    return {"item_id": item_id}
```

业务错误不要直接 `return {"error": ...}`，优先使用合适的 HTTP 状态码。

## 依赖注入 Depends

`Depends` 用于把公共逻辑抽出来，例如分页参数、鉴权、数据库会话。

```python
from typing import Annotated

from fastapi import Depends, FastAPI

app = FastAPI()


def pagination(page: int = 1, size: int = 20):
    return {"page": page, "size": size}


@app.get("/items")
def list_items(paging: Annotated[dict, Depends(pagination)]):
    return paging
```

适合放进依赖的内容：

- 当前登录用户。
- 数据库 Session。
- 权限检查。
- 分页、排序、过滤参数。
- 外部服务客户端。

## async 与 def 的选择

FastAPI 同时支持 `def` 和 `async def`。

使用 `async def` 的场景：

- 调用异步数据库驱动。
- 调用异步 HTTP 客户端。
- 使用 `await` 的异步 I/O。

使用普通 `def` 的场景：

- 主要是 CPU 计算。
- 调用同步数据库驱动。
- 调用同步 SDK 或阻塞函数。

不要为了看起来高级而强行使用 `async`。如果函数内部没有 `await`，普通 `def` 往往更清晰。

## APIRouter 拆分路由

项目变大后，不要把所有接口都写在 `main.py`。

示例结构：

```text
my_fastapi_project/
├── app/
│   ├── __init__.py
│   ├── main.py
│   ├── api/
│   │   ├── __init__.py
│   │   ├── items.py
│   │   └── users.py
│   ├── core/
│   │   └── config.py
│   ├── models/
│   │   └── user.py
│   ├── schemas/
│   │   └── user.py
│   ├── services/
│   │   └── user_service.py
│   └── db/
│       └── session.py
├── tests/
│   └── test_api.py
├── pyproject.toml
└── .env
```

`app/api/users.py`：

```python
from fastapi import APIRouter

router = APIRouter(prefix="/users", tags=["users"])


@router.get("/{user_id}")
def get_user(user_id: int):
    return {"user_id": user_id}
```

`app/main.py`：

```python
from fastapi import FastAPI

from app.api import users

app = FastAPI(title="Demo API")
app.include_router(users.router)
```

## schemas、models、services 的区别

常见分层：

- `schemas`：接口层数据结构，通常是 Pydantic 模型，用于请求参数和响应结果校验。
- `models`：数据库模型，通常对应数据库表。
- `services`：业务逻辑，不直接关心 HTTP 请求细节。
- `api` 或 `routers`：路由层，负责接收请求、调用 service、返回响应。
- `db`：数据库连接、Session、迁移相关配置。

关键点：`schemas` 不等于数据库表，它更像“接口输入输出契约”。

## 数据库基本模式

FastAPI 不强制绑定 ORM。常见选择：

- SQLModel：和 FastAPI、Pydantic 风格接近，适合学习和中小项目。
- SQLAlchemy：生态成熟，适合复杂数据库场景。
- Tortoise ORM、Piccolo、Beanie：异步或特定数据库场景。

典型数据库 Session 依赖：

```python
from collections.abc import Generator


def get_db() -> Generator:
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()
```

路由中使用：

```python
from typing import Annotated

from fastapi import Depends


@app.get("/users")
def list_users(db: Annotated[Session, Depends(get_db)]):
    return db.query(User).all()
```

## 配置管理

配置不要硬编码在业务代码中。常见做法是使用 Pydantic Settings 或环境变量。

```python
from pydantic_settings import BaseSettings


class Settings(BaseSettings):
    app_name: str = "Demo API"
    database_url: str

    class Config:
        env_file = ".env"


settings = Settings()
```

`.env` 示例：

```text
DATABASE_URL=sqlite:///./app.db
```

## CORS

前后端分离时，浏览器跨域请求通常需要配置 CORS。

```python
from fastapi.middleware.cors import CORSMiddleware

app.add_middleware(
    CORSMiddleware,
    allow_origins=["http://localhost:5173"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)
```

生产环境不要随意使用 `allow_origins=["*"]` 配合凭证请求。

## 后台任务

适合处理“不影响本次响应”的轻量任务，例如发送邮件、写日志。

```python
from fastapi import BackgroundTasks


def write_log(message: str):
    with open("app.log", "a", encoding="utf-8") as f:
        f.write(message + "\n")


@app.post("/notify")
def notify(background_tasks: BackgroundTasks):
    background_tasks.add_task(write_log, "notify called")
    return {"ok": True}
```

重量级任务应使用 Celery、RQ、Dramatiq 或消息队列，不要塞进 FastAPI 进程。

## 测试

FastAPI 提供 `TestClient`，可以不启动真实服务器直接测试接口。

```python
from fastapi.testclient import TestClient

from app.main import app

client = TestClient(app)


def test_read_root():
    response = client.get("/")
    assert response.status_code == 200
    assert response.json() == {"message": "Hello, FastAPI!"}
```

运行：

```bash
pytest
```

测试重点：

- 正常请求是否返回预期数据。
- 参数错误时是否返回 422 或业务状态码。
- 鉴权失败是否返回 401 或 403。
- 数据库写入是否可回滚或隔离。

## 常见问题

### 422 Unprocessable Entity

通常是请求参数不符合类型或校验规则。

排查：

- 路径参数、查询参数、请求体字段名是否一致。
- 请求体是否是 JSON。
- `Content-Type` 是否为 `application/json`。
- Pydantic 模型字段是否缺少默认值。

### 路由顺序冲突

固定路径应放在动态路径前面：

```python
@app.get("/users/me")
def read_me():
    return {"user": "me"}


@app.get("/users/{user_id}")
def read_user(user_id: str):
    return {"user_id": user_id}
```

如果先声明 `/users/{user_id}`，`/users/me` 可能会被当成动态参数处理。

### response_model 与返回值不一致

如果返回数据不符合 `response_model`，FastAPI 会尝试过滤和转换；严重不匹配时会报错。应让 service 返回清晰的数据对象，再由 schema 控制输出。

### 同步阻塞影响性能

在 `async def` 里直接调用阻塞 I/O，例如同步 requests、同步数据库驱动，可能阻塞事件循环。要么使用同步 `def`，要么换成异步库。

## 学习路线

- [ ] 跑通最小示例和 `/docs`。
- [ ] 掌握路径参数、查询参数、请求体。
- [ ] 掌握 Pydantic 模型和响应模型。
- [ ] 掌握 `HTTPException` 和状态码。
- [ ] 掌握 `Depends` 依赖注入。
- [ ] 用 `APIRouter` 拆分一个小项目。
- [ ] 接入 SQLite 或 PostgreSQL。
- [ ] 编写 `TestClient` 接口测试。
- [ ] 配置 CORS、环境变量、日志。
- [ ] 学习部署：Uvicorn、Gunicorn、Docker、反向代理。

## 相关链接

- [[uv使用指南]]
- [[项目配置]]
