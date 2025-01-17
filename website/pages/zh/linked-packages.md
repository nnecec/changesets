# 关联包

> 该文档翻译已由[@nnecec](https://github.com/nnecec)修订

关联包允许你指定一个或多个应该一起进行版本控制的包组。由于存在一些复杂的情况，下面通过几个示例展示了不同的情况。

- 关联包仅在有针对它们的 Changeset 时才会被升级（这可以意味着你明确选择为它添加 Changeset 或因为它是一个正在发布的项目的依赖）。
- 如果关联包组中的包有 Changeset，则这些包**总是**会被版本化到关联包组中当前最高版本 + 组内 Changeset 中最高的升级类型。

> 与 `固定包` 不同，并不能保证关联包组中的所有包都会进行版本升级并发布，只有那些有 Changeset 的包会被升级。

## 示例

### 一般示例

假设有三个包，分别是 `pkg-a`、`pkg-b` 和 `pkg-c`。其中 `pkg-a` 和 `pkg-b` 是关联的，而 `pkg-c` 不是，因此配置如下所示：

```json
{
  "linked": [["pkg-a", "pkg-b"]]
}
```

- `pkg-a` 版本为 `1.0.0`
- `pkg-b` 版本为 `1.0.0`
- `pkg-c` 版本为 `1.0.0`

如果我有一个 Changeset 对 `pkg-a` 进行了补丁更新，对 `pkg-b` 进行了次要版本更新，并对 `pkg-c` 进行了主要版本更新，然后我进行了发布，那么最终的版本将会是：

- `pkg-a` 版本为 `1.1.0`
- `pkg-b` 版本为 `1.1.0`
- `pkg-c` 版本为 `2.0.0`

现在我又有了一个对 `pkg-a` 进行次要版本更新的 Changeset，然后我进行了发布，那么最终的版本将会是：

- `pkg-a` 版本为 `1.2.0`
- `pkg-b` 版本为 `1.1.0`
- `pkg-c` 版本为 `2.0.0`

现在我又有了一个对 `pkg-b` 进行次要版本更新的 Changeset，然后我进行了发布，那么最终的版本将会是：

- `pkg-a` 版本为 `1.2.0`
- `pkg-b` 版本为 `1.3.0`
- `pkg-c` 版本为 `2.0.0`

现在我又有了一个对所有三个包都进行补丁更新的 Changeset，然后我进行了发布，那么最终的版本将会是：

- `pkg-a` 版本为 `1.3.1`
- `pkg-b` 版本为 `1.3.1`
- `pkg-c` 版本为 `2.0.1`

### 带有依赖的示例

假设有两个包 `pkg-a` 和 `pkg-b`，它们是关联的。`pkg-a` 依赖于 `pkg-b`。

```json
{
  "linked": [["pkg-a", "pkg-b"]]
}
```

- `pkg-a` 版本为 `1.0.0`
- `pkg-b` 版本为 `1.0.0`

如果我有一个对 `pkg-b` 进行主要版本更新的 Changeset，然后我进行了发布，那么最终的版本将会是：

- `pkg-a` 版本为 `2.0.0`
- `pkg-b` 版本为 `2.0.0`

现在我又有了一个对 `pkg-a` 进行主要版本更新的 Changeset，然后我进行了发布，那么最终的版本将会是：

- `pkg-a` 版本为 `3.0.0`
- `pkg-b` 版本为 `2.0.0`

## 使用通配符表达式

有时候你可能希望在一个项目中（例如在一个 monorepo 设置中）关联很多或所有的包，在这种情况下你需要保持关联包列表的最新状态。

为了简化维护这个列表的工作，你可以在关联列表中提供通配符表达式，这些表达式将匹配并解析为你想要包含的所有包。

例如：

```json
{
  "linked": [["pkg-*"]]
}
```

它将匹配所有以 `pkg-` 开头的包。

**通配符表达式必须按照 [micromatch](https://www.npmjs.com/package/micromatch) 的格式定义。**
