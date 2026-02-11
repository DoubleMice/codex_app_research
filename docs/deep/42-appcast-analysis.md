# Appcast XML 更新源分析

## 概述

Codex Desktop 使用 Sparkle 框架进行自动更新，通过 appcast.xml 获取版本信息。

**URL**: `https://persistent.oaistatic.com/codex-app-prod/appcast.xml`
**格式**: RSS 2.0 + Sparkle 命名空间

## 1. XML 结构

```xml
<?xml version="1.0" standalone="yes"?>
<rss xmlns:sparkle="http://www.andymatuschak.org/xml-namespaces/sparkle" version="2.0">
    <channel>
        <title>Codex</title>
        <item>
            <!-- 版本条目 -->
        </item>
    </channel>
</rss>
```

## 2. 版本条目结构

```xml
<item>
    <title>260208.1016</title>
    <pubDate>Sun, 08 Feb 2026 18:21:54 +0000</pubDate>
    <sparkle:version>571</sparkle:version>
    <sparkle:shortVersionString>260208.1016</sparkle:shortVersionString>
    <sparkle:minimumSystemVersion>12.0</sparkle:minimumSystemVersion>
    <enclosure
        url="https://persistent.oaistatic.com/codex-app-prod/Codex-darwin-arm64-260208.1016.zip"
        length="150067768"
        type="application/octet-stream"
        sparkle:edSignature="btcea0RdShDRUusmfzTeybj95o1lz66uda/lJ/uvs70CuhtbBsKG3eDKXRWS5ScPRLFHMGPpD6hnmWRg4eBRBw=="/>
    <sparkle:deltas>
        <!-- 增量更新 -->
    </sparkle:deltas>
</item>
```

## 3. 版本历史

| 版本号 | 构建号 | 发布日期 | 大小 |
|--------|--------|----------|------|
| 260208.1016 | 571 | 2026-02-08 | 150 MB |
| 260206.1448 | 565 | 2026-02-06 | 148 MB |
| 260206.1259 | 563 | 2026-02-06 | 148 MB |
| 260205.1301 | 554 | 2026-02-05 | 148 MB |
| 260205.0946 | 552 | 2026-02-05 | 148 MB |

## 4. 下载 URL 模式

### 完整包

```
https://persistent.oaistatic.com/codex-app-prod/Codex-darwin-arm64-{version}.zip
```

### 增量更新 (Delta)

```
https://persistent.oaistatic.com/codex-app-prod/Codex{to}-{from}.delta
```

## 5. 增量更新示例

```xml
<sparkle:deltas>
    <enclosure
        url="https://persistent.oaistatic.com/codex-app-prod/Codex571-565.delta"
        sparkle:deltaFrom="565"
        length="1419238"
        type="application/octet-stream"
        sparkle:deltaFromSparkleExecutableSize="860528"
        sparkle:deltaFromSparkleLocales="de,ar,el,ja,uk,zh_CN,nb"
        sparkle:edSignature="AC3gfI916PEdnKZg0VeYuOxuXQwf0E+NP6qGEje7rISu+uTwvVRCbUbP7ejYPnCrnugeE1GnTo9XUD5mzqcCCw=="/>
</sparkle:deltas>
```

## 6. Delta 更新大小对比

| 从版本 | 到版本 | Delta 大小 | 完整包大小 | 节省 |
|--------|--------|------------|------------|------|
| 565 | 571 | 1.4 MB | 150 MB | 99% |
| 563 | 571 | 1.4 MB | 150 MB | 99% |
| 554 | 571 | 6.1 MB | 150 MB | 96% |
| 552 | 571 | 6.1 MB | 150 MB | 96% |
| 531 | 571 | 7.6 MB | 150 MB | 95% |

## 7. 签名验证

### EdDSA 签名

```
sparkle:edSignature="btcea0RdShDRUusmfzTeybj95o1lz66uda/lJ/uvs70CuhtbBsKG3eDKXRWS5ScPRLFHMGPpD6hnmWRg4eBRBw=="
```

- **算法**: EdDSA (Ed25519)
- **编码**: Base64
- **用途**: 验证下载包完整性

## 8. 系统要求

```xml
<sparkle:minimumSystemVersion>12.0</sparkle:minimumSystemVersion>
```

- **最低版本**: macOS 12.0 (Monterey)

## 9. 本地化支持

Delta 更新包含的语言:

```
de,ar,el,ja,uk,zh_CN,nb
```

| 代码 | 语言 |
|------|------|
| de | 德语 |
| ar | 阿拉伯语 |
| el | 希腊语 |
| ja | 日语 |
| uk | 乌克兰语 |
| zh_CN | 简体中文 |
| nb | 挪威语 |

## 10. Sparkle 命名空间

```
xmlns:sparkle="http://www.andymatuschak.org/xml-namespaces/sparkle"
```

### 使用的属性

| 属性 | 说明 |
|------|------|
| sparkle:version | 内部构建号 |
| sparkle:shortVersionString | 显示版本号 |
| sparkle:minimumSystemVersion | 最低系统版本 |
| sparkle:edSignature | EdDSA 签名 |
| sparkle:deltaFrom | Delta 来源版本 |
| sparkle:deltaFromSparkleExecutableSize | 可执行文件大小 |
| sparkle:deltaFromSparkleLocales | 本地化语言 |

## 11. 更新流程

1. 应用启动时检查 appcast.xml
2. 比较当前版本与最新版本
3. 如果有 delta 更新可用，优先下载 delta
4. 验证 EdDSA 签名
5. 应用更新并重启

## 12. CDN 配置

- **域名**: persistent.oaistatic.com
- **路径**: /codex-app-prod/
- **协议**: HTTPS

