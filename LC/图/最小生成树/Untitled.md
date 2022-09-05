```go
/*  serviceLabels := map[string]string{"prefix": "backend.db", "service": serviceName}   methodLabels := map[string]string{"prefix": "backend.db", "service": serviceName, "method": action}   xmetrics.PrivateHistogram("total_timing", "",nil, serviceLabels).Observe(float64(costInMicrosecond))   xmetrics.PrivateHistogram("method_timing", "",nil, methodLabels).Observe(float64(costInMicrosecond))   xmetrics.PrivateCounter("total_num", "", serviceLabels).Add(1)   xmetrics.PrivateCounter("method_num", "", methodLabels).Add(1)   if err != nil && err != sql.ErrNoRows {      xmetrics.PrivateCounter("total_failed_num", "", serviceLabels).Add(1)      xmetrics.PrivateCounter("method_failed_num", "", methodLabels).Add(1)   }*/
```

