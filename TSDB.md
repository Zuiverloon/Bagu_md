# prometheus

监控告警工具包

## 架构

prometheus server：从接口抓取数据，存在时序 db 中，处理查询
exporter：暴露一个 endpoint 给 server

## 数据结构

时间序列（Time Series）
每条时间序列由一个唯一的标识符组成：metric name + label set

每个时间序列是一个有序的数据点列表：(timestamp, value)

数据点是以 float64 存储的数值，时间戳是 int64

# Opentelemetry

可观测性的解决方案，
定义了 signal 的格式，traces/metrics/log
有一个 collector 会负责 signal 采集/处理/导出到下游后端服务如 grafana，datadog，es

# 常用数据结构

1. 列式存储
1. 按照时间戳分区
1. 倒排索引（关键词出现在哪些行中）
1. 位图索引
1. segment tree/fenwick tree
