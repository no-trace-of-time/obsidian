# Readme
Interact with Gogs servers via _curl_ requests to their [REST API v1](https://github.com/gogs/docs-api).

Use `./gogs` to show available actions.  
Use `./gogs <action>` to show required parameters.  
Use `./gogs <action> <argument> ...` to run actions.

## Available actions include to[](http://nas.home:3010/gogs/gogs-cli#available-actions-include-to)

-   create/delete/list/query user/organization **repositories**
-   add/remove/list repository **collaborators**
-   create/list organization **teams**
-   add/remove/list organization **team members**
-   add/remove organization **team repositories**
-   fetch **raw** file content

## Setup[](http://nas.home:3010/gogs/gogs-cli#setup)

-   Gogs server URL
    -   Required in the form `https://try.gogs.io`
    -   Via environment variable or file in this directory
-   Gogs API token
    -   Create at `https://YOUR.GOGS.SERVER/user/settings/applications`
    -   Via environment variable or file in this directory

(See `config` section in `./gogs`)

# 实践
## 配置信息
在gogs-cli目录下，创建gogs-server-url.txt,gogs-api-token.txt

# 概念
- 用户 User
- 组织 Org
- 仓库 Repo
并不是三层结构，应当是用户或者组织、仓库，这种两层结构

# 功能
### 查看用户名下repo
```
./gogs list-user-repos simonxu72
```

## 查看仓库下的repo
```
./gogs list-org-repos ErlangBuild
```

## 查看team下的repo
```
./gogs list-teams gogs
```

## 删除repo
```
./gogs delete-repo inaka aaa
```

- 之前一直用delete-repo simonxu72/ErlangBuild otp_vsn，一直404，最后，发现用户和组织是基本平行的，需要用delete-repo simonxu72 otp_vsn，或者delete-repo ErlangBuild otp_vsn，才能成功

## 创建repo，org下，或者user下
```
./gogs create-org-repo  inaka repoa desc true
```

- [ ] 但是delete-repo inaka repoa成功但是delete-repo simonxu72 repoa失败了

# 新功能
## 创建org
```
actions=$actions" create-org"
function create-org {
	syntax-help 5 "user-name org-name full-name description website location " "$@"
	gogs-curl POST /admin/users/$1/orgs "{\"username\":\"$2\",\"fullname\":\"$3\",\"description\":\"$4\",\"website\":\"$5\",\"location\":\      "$6\"}"
}
```

```
./gogs create-org simonxu72 inaka inaka desc site loc
```
原来代码不能创建org

