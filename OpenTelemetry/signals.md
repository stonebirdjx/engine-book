官方链接： https://opentelemetry.io/zh/docs/concepts/signals/
# 一般流程
- Data source specific configuration
- Exporter configuration
- Propagator configuration
- Resource configuration 

# Traces
 provider、exporter、tracer：
 - span：
    - Name
    - Parent span ID (empty for root spans)
    - Start and End Timestamps
    - Span Context
    - Attributes
    - Span Events
    - Span Links
    - Span Status
 ```go
ctx, span := tracer.Start(r.Context(), "roll")
defer span.End()
 ```
# Metrics
provider、exporter、meter：
- metric{Name、Kind、Unit (optional)、Description (optional)}
    - Counter: 只增不减（单一系统）
    - Asynchronous Counter: (分布式系统)
    - UpDownCounter: 可增可减
    - Asynchronous UpDownCounter:(分布式系统)
    - Gauge: 可增可减
    - Histogram: 统计数据
# Logs
provider、exporter、logger：
一些kv元数据信息

# Baggage
信号之间传递的上下文信息。