# StreamShield Proxy 配置

此文件夹包含 StreamShield Proxy 的配置文件和 M3U 源。

## 文件结构

```
config/
├── proxy_hosts/
│   ├── default.txt         # 默认代理 hosts 列表 (默认创建，每次升级会覆盖)
│   └── user_defined.txt    # 用户自定义代理 hosts 列表 (预创建，用户编辑，升级不会覆盖)
├── remote_m3u/
│   ├── no_proxy/
│   │   ├── default_sources.txt  # 默认非代理 M3U 源列表 (默认创建，每次升级会覆盖)
│   │   └── sources.txt          # 用户自定义非代理 M3U 源列表 (预创建，用户编辑，升级不会覆盖)
│   └── proxy_needed/
│       └── sources.txt          # 用户自定义代理 M3U 源列表 (预创建，用户编辑，升级不会覆盖)
├── local_m3u/
│   ├── no_proxy/
│   │   └── user_m3u.m3u         # 用户本地非代理 M3U 文件 (用户手动添加，文件名随便取，后缀支持m3u和txt)
│   └── proxy_needed/
│       └── user_m3u.m3u         # 用户本地代理 M3U 文件 (用户手动添加，文件名随便取，后缀支持m3u和txt)
└── generated/
    └── all.m3u                  # 聚合的 M3U 文件 (程序自动生成)
```

## 使用说明

1. 用户可以在 `user_defined.txt` 中添加自定义的代理 hosts。
2. 在 `remote_m3u/no_proxy/sources.txt` 和 `remote_m3u/proxy_needed/sources.txt` 中添加远程 M3U 源。
3. 用户可以在 `local_m3u` 文件夹中添加本地 M3U 文件。
4. `generated/all.m3u` 是程序自动生成的聚合 M3U 文件，请勿手动编辑。

注意：除了 `default.txt` 和 `default_sources.txt`，其他文件在升级时不会被覆盖。
