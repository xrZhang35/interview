#### 1. **全文搜索（Full-text Search）**

- 支持复杂查询语法（match, bool, multi_match 等）
- 基于倒排索引 + 分词器（如 IK, standard, whitespace）
- 支持打分（relevance score）和排序

#### 2. **结构化搜索（Structured Search）**

- 类似数据库的精确匹配查询（term、range）
- 多条件组合查询（bool 查询）
- 支持嵌套字段（nested）、对象字段、数组等复杂数据结构

#### 3. **实时近似搜索（Near Real-Time）**

- 新写入的数据在 **refresh interval（默认1秒）** 后即可搜索
- 提供快速的用户体验，尤其适合日志、监控场景

#### 4. **聚合分析（Aggregations）**

- 类似 SQL 的 `GROUP BY` + 聚合函数（sum、avg、max、min、terms、date_histogram）
- 支持层级聚合，用于做统计报表、数据可视化
- 是 ELK Stack 中 Kibana 图表背后的支撑能力

#### 5. **分布式与高可用**

- 支持水平扩展，自动分片、自动负载均衡
- 副本机制实现高可用（主副分片）
- Master 节点负责集群状态协调与选举

#### 6. **多语言支持**

- 提供多语言客户端：Java、Python、Go、JS、.NET 等
- Java 中通常使用 `RestHighLevelClient` 或更轻量的 `RestClient`

#### 7. **复杂的排序和打分机制**

- 基于 BM25 算法
- 支持 Function Score、Script Score 等自定义打分策略

#### 8. **数据生命周期管理（ILM）**

- 热-温-冷数据分层存储
- 可根据数据生命周期自动切换存储策略、合并、删除旧数据

#### 9. **安全性与权限控制（X-Pack）**

- 用户认证、权限控制、TLS 加密、审计日志等（企业版功能）

---

Elasticsearch 不仅是一个搜索引擎，更是一个 **分布式的数据分析平台**，支持实时搜索、聚合分析与大数据处理，适用于：

- 日志分析（ELK）
- 电商搜索
- 运维监控
- 推荐系统
- 数据仓库/BI 查询加速