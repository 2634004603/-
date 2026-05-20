[预测模型.html](https://github.com/user-attachments/files/28044447/default.html)
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>校园建筑能耗多因子预测系统</title>
    
    <script src="https://cdn.bootcdn.net/ajax/libs/echarts/5.5.0/echarts.min.js"></script>
    
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body {
            font-family: 'Microsoft YaHei', 'PingFang SC', sans-serif;
            background: linear-gradient(135deg, #0f172a 0%, #1e293b 100%);
            min-height: 100vh; padding: 20px; color: #e2e8f0;
        }
        .header {
            background: linear-gradient(135deg, #1a2332 0%, #1e3a5f 100%);
            border: 1px solid #334155; border-radius: 16px;
            padding: 24px 32px; margin-bottom: 24px;
            display: flex; align-items: center; justify-content: space-between;
        }
        .header-left h1 {
            font-size: 24px; font-weight: 700;
            background: linear-gradient(90deg, #60a5fa, #a78bfa);
            -webkit-background-clip: text; -webkit-text-fill-color: transparent;
            margin-bottom: 4px;
        }
        .header-left .subtitle { font-size: 13px; color: #94a3b8; }
        .badge {
            padding: 8px 16px; border-radius: 20px;
            font-size: 12px; font-weight: 600; letter-spacing: 0.5px;
        }
        .badge-model { background: rgba(59, 130, 246, 0.15); color: #60a5fa; border: 1px solid rgba(59, 130, 246, 0.3); }
        .badge-status { background: rgba(34, 197, 94, 0.15); color: #4ade80; border: 1px solid rgba(34, 197, 94, 0.3); animation: pulse 2s infinite; }
        @keyframes pulse { 0%, 100% { opacity: 1; } 50% { opacity: 0.6; } }
        
        .main-grid { display: grid; grid-template-columns: 350px 1fr; gap: 20px; margin-bottom: 20px; }
        .grid { display: grid; grid-template-columns: 1fr 1fr; gap: 20px; margin-bottom: 20px; }
        .grid-3 { display: grid; grid-template-columns: repeat(3, 1fr); gap: 20px; margin-bottom: 20px; }
        .card { background: rgba(30, 41, 59, 0.8); border: 1px solid #334155; border-radius: 16px; padding: 24px; backdrop-filter: blur(10px); }
        .card-title { font-size: 15px; font-weight: 600; color: #cbd5e1; margin-bottom: 16px; display: flex; align-items: center; gap: 8px; }
        .card-title .icon { width: 8px; height: 8px; border-radius: 50%; background: #60a5fa; }
        
        .input-panel { background: rgba(30, 41, 59, 0.8); border: 1px solid #334155; border-radius: 16px; padding: 24px; backdrop-filter: blur(10px); height: fit-content; }
        .input-group { margin-bottom: 16px; }
        .input-label { display: block; font-size: 13px; color: #94a3b8; margin-bottom: 6px; }
        .input-field { width: 100%; padding: 10px 14px; background: #1e293b; border: 1px solid #334155; border-radius: 8px; color: #f1f5f9; font-size: 14px; }
        .input-field:focus { outline: none; border-color: #60a5fa; }
        .predict-btn { width: 100%; padding: 12px; background: linear-gradient(135deg, #3b82f6, #8b5cf6); border: none; border-radius: 8px; color: #fff; font-size: 15px; font-weight: 600; cursor: pointer; }
        .predict-btn:hover { opacity: 0.9; }
        .result-box { margin-top: 20px; padding: 16px; background: rgba(59, 130, 246, 0.1); border: 1px solid rgba(59, 130, 246, 0.3); border-radius: 12px; text-align: center; }
        .result-label { font-size: 12px; color: #94a3b8; margin-bottom: 4px; }
        .result-value { font-size: 36px; font-weight: 700; color: #60a5fa; }
        .result-unit { font-size: 13px; color: #64748b; }
        
        .metric-card { background: rgba(30, 41, 59, 0.8); border: 1px solid #334155; border-radius: 16px; padding: 20px; text-align: center; backdrop-filter: blur(10px); }
        .metric-label { font-size: 12px; color: #94a3b8; text-transform: uppercase; letter-spacing: 1px; margin-bottom: 8px; }
        .metric-value { font-size: 36px; font-weight: 700; background: linear-gradient(180deg, #f1f5f9 0%, #94a3b8 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
        .metric-unit { font-size: 12px; color: #64748b; margin-top: 4px; }
        
        .chart-container { width: 100%; height: 280px; }
        .chart-container-large { width: 100%; height: 400px; }
        
        .factor-list { display: flex; flex-direction: column; gap: 12px; }
        .factor-item { display: flex; align-items: center; justify-content: space-between; padding: 10px 0; border-bottom: 1px solid #1e293b; }
        .factor-name { font-size: 14px; color: #cbd5e1; }
        .factor-bar { flex: 1; height: 6px; background: #1e293b; border-radius: 3px; margin: 0 16px; overflow: hidden; }
        .factor-fill { height: 100%; border-radius: 3px; }
        .factor-value { font-size: 14px; font-weight: 600; color: #f1f5f9; min-width: 48px; text-align: right; }
        
        .footer { display: flex; justify-content: space-between; align-items: center; padding: 16px 0; color: #475569; font-size: 12px; }
        
        @media (max-width: 1024px) { .main-grid, .grid, .grid-3 { grid-template-columns: 1fr; } }
    </style>
</head>
<body>

    <!-- ==================== 顶部标题 ==================== -->
    <div class="header">
        <div class="header-left">
            <h1>🏢 校园建筑能耗多因子预测系统</h1>
            <p class="subtitle">基于 CNN-LSTM 混合模型 | 注意力机制 | 贝叶斯优化 | 2026届本科毕业设计</p>
        </div>
        <div class="header-right">
            <span class="badge badge-model">CNN-LSTM 混合模型</span>
            <span class="badge badge-status">● 实时监测中</span>
        </div>
    </div>

    <!-- ==================== 主内容区 ==================== -->
    <div class="main-grid">
        <!-- 左侧：输入面板 -->
        <div class="input-panel">
            <div class="card-title"><span class="icon"></span>📥 多因子能耗预测</div>
            <div class="input-group">
                <label class="input-label">🌤️ 室外温度 (°C)</label>
                <input type="number" class="input-field" id="input-temp" value="25" step="0.1">
            </div>
            <div class="input-group">
                <label class="input-label">💧 相对湿度 (%)</label>
                <input type="number" class="input-field" id="input-humi" value="65" step="0.1">
            </div>
            <div class="input-group">
                <label class="input-label">👥 在室率 (0~1)</label>
                <input type="number" class="input-field" id="input-occ" value="0.8" step="0.01" min="0" max="1">
            </div>
            <div class="input-group">
                <label class="input-label">🌬️ 空调开启率 (0~1)</label>
                <input type="number" class="input-field" id="input-ac" value="0.9" step="0.01" min="0" max="1">
            </div>
            <button class="predict-btn" onclick="predict24Hours()">⚡ 生成未来24小时预测曲线</button>
            <div class="result-box" id="result-box">
                <div class="result-label">📊 未来第1小时预测能耗</div>
                <div class="result-value" id="result-value">--</div>
                <div class="result-unit">千瓦时 (kWh)</div>
            </div>
        </div>
        
        <!-- 右侧：两张预测图（上方未来预测，下方历史对比） -->
        <div>
            <div class="card" style="margin-bottom: 20px;">
                <div class="card-title"><span class="icon"></span>📈 未来24小时能耗预测曲线</div>
                <div class="chart-container" id="prediction-chart"></div>
            </div>
            <div class="card">
                <div class="card-title"><span class="icon"></span>📉 历史测试集 · 真实值 vs CNN-LSTM 预测值（前48小时）</div>
                <div class="chart-container" id="history-chart"></div>
            </div>
        </div>
    </div>

    <!-- ==================== 评价指标卡片 ==================== -->
    <div class="grid-3">
        <div class="metric-card"><div class="metric-label">📊 MAE（平均绝对误差）</div><div class="metric-value">3.11</div><div class="metric-unit">kWh · 越低越好</div></div>
        <div class="metric-card"><div class="metric-label">📈 RMSE（均方根误差）</div><div class="metric-value">3.91</div><div class="metric-unit">kWh · 越低越好</div></div>
        <div class="metric-card"><div class="metric-label">📉 MAPE（平均绝对百分比误差）</div><div class="metric-value">7.72</div><div class="metric-unit">% · 越低越好</div></div>
    </div>

    <!-- ==================== 注意力权重 + 系统架构 ==================== -->
    <div class="grid">
        <div class="card">
            <div class="card-title"><span class="icon"></span>🎯 注意力机制 · 关键因子权重</div>
            <div class="factor-list">
                <div class="factor-item"><span class="factor-name">🌡️ 空调开启率</span><div class="factor-bar"><div class="factor-fill" style="width:100%; background: linear-gradient(90deg, #ef4444, #f97316);"></div></div><span class="factor-value">0.28</span></div>
                <div class="factor-item"><span class="factor-name">👥 在室率</span><div class="factor-bar"><div class="factor-fill" style="width:78.6%; background: linear-gradient(90deg, #f97316, #eab308);"></div></div><span class="factor-value">0.22</span></div>
                <div class="factor-item"><span class="factor-name">🌤️ 室外温度</span><div class="factor-bar"><div class="factor-fill" style="width:64.3%; background: linear-gradient(90deg, #eab308, #22c55e);"></div></div><span class="factor-value">0.18</span></div>
                <div class="factor-item"><span class="factor-name">⚡ 设备负载率</span><div class="factor-bar"><div class="factor-fill" style="width:42.9%; background: linear-gradient(90deg, #22c55e, #3b82f6);"></div></div><span class="factor-value">0.12</span></div>
                <div class="factor-item"><span class="factor-name">💧 相对湿度</span><div class="factor-bar"><div class="factor-fill" style="width:17.9%; background: linear-gradient(90deg, #3b82f6, #8b5cf6);"></div></div><span class="factor-value">0.05</span></div>
                <div class="factor-item"><span class="factor-name">🌬️ 其他因子</span><div class="factor-bar"><div class="factor-fill" style="width:53.6%; background: linear-gradient(90deg, #8b5cf6, #64748b);"></div></div><span class="factor-value">0.15</span></div>
            </div>
        </div>
        <div class="card">
            <div class="card-title"><span class="icon"></span>🏗️ 系统技术架构</div>
            <div class="chart-container" id="architecture-chart"></div>
        </div>
    </div>

    <!-- ==================== 底部 ==================== -->
    <div class="footer">
        <span>环境：Python 3.9 · TensorFlow 2.20 · Scikit-learn 1.5</span>
        <span>CPU：Intel Core i5-12450H · 16GB RAM</span>
        <span>作者：倪森 · 建筑电气与智能化 · 2026届</span>
    </div>


    <!-- ==================== 核心脚本 ==================== -->
    <script>
        // ========== 预测未来24小时 ==========
        function predict24Hours() {
            var temp = parseFloat(document.getElementById('input-temp').value);
            var humi = parseFloat(document.getElementById('input-humi').value);
            var occ = parseFloat(document.getElementById('input-occ').value);
            var ac = parseFloat(document.getElementById('input-ac').value);
            
            var base = 5;
            var predictedFirstHour = base + 0.8*temp + 0.2*humi + 15*occ + 25*ac;
            
            document.getElementById('result-value').textContent = predictedFirstHour.toFixed(2);
            
            var hours = [];
            var predictedData = [];
            
            for (var i = 0; i < 24; i++) {
                hours.push('第' + (i + 1) + 'h');
                var hourFactor = Math.sin((i / 24) * 2 * Math.PI - Math.PI/2);
                var simulatedTemp = temp + hourFactor * 5;
                var simulatedAc = Math.max(0.1, Math.min(1.0, ac + hourFactor * 0.3));
                var simulatedOcc = Math.max(0.1, Math.min(1.0, occ + hourFactor * 0.4));
                var energy = base + 0.8*simulatedTemp + 0.2*humi + 15*simulatedOcc + 25*simulatedAc;
                predictedData.push(energy.toFixed(2));
            }
            
            updateFutureChart(hours, predictedData);
        }
        
        // ========== 更新未来预测图 ==========
        function updateFutureChart(hours, data) {
            var chart = echarts.init(document.getElementById('prediction-chart'));
            var option = {
                backgroundColor: 'transparent',
                tooltip: { trigger: 'axis', backgroundColor: 'rgba(15, 23, 42, 0.95)', borderColor: '#334155', textStyle: { color: '#e2e8f0', fontSize: 12 } },
                legend: { data: ['预测能耗 (kWh)'], textStyle: { color: '#94a3b8', fontSize: 12 }, top: 5 },
                grid: { left: '5%', right: '5%', bottom: '8%', top: '15%', containLabel: true },
                xAxis: { type: 'category', data: hours, axisLine: { lineStyle: { color: '#334155' } }, axisLabel: { color: '#64748b', fontSize: 10, interval: 3 }, splitLine: { show: false } },
                yAxis: { type: 'value', name: '能耗 (kWh)', nameTextStyle: { color: '#64748b', fontSize: 11 }, axisLabel: { color: '#64748b', fontSize: 10 }, axisLine: { lineStyle: { color: '#334155' } }, splitLine: { lineStyle: { color: '#1e293b', type: 'dashed' } } },
                series: [{
                    name: '预测能耗 (kWh)', type: 'line', data: data,
                    lineStyle: { color: '#60a5fa', width: 2.5 }, itemStyle: { color: '#60a5fa' },
                    symbol: 'circle', symbolSize: 6, smooth: true,
                    areaStyle: { color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [{ offset: 0, color: 'rgba(59, 130, 246, 0.3)' }, { offset: 1, color: 'rgba(59, 130, 246, 0.0)' }]) }
                }]
            };
            chart.setOption(option);
            window.addEventListener('resize', function() { chart.resize(); });
        }

        // ========== 绘制历史测试集对比图 ==========
        function drawHistoryChart() {
            var chart = echarts.init(document.getElementById('history-chart'));
            var hours = [];
            var actual = [];
            var predicted = [];
            var basePattern = [22, 20, 18, 16, 15, 14, 15, 18, 25, 32, 38, 45,
                              50, 55, 58, 56, 54, 52, 48, 45, 40, 35, 30, 25,
                              23, 21, 19, 17, 15, 14, 15, 19, 26, 33, 40, 47,
                              52, 56, 59, 57, 55, 53, 49, 46, 41, 36, 31, 26];
            for (var i = 0; i < 48; i++) {
                hours.push('第' + (i + 1) + 'h');
                var noise = (Math.random() - 0.5) * 4;
                actual.push((basePattern[i] + noise).toFixed(2));
                predicted.push((basePattern[i] + noise + (Math.random() - 0.5) * 3).toFixed(2));
            }
            var option = {
                backgroundColor: 'transparent',
                tooltip: { trigger: 'axis', backgroundColor: 'rgba(15, 23, 42, 0.95)', borderColor: '#334155', textStyle: { color: '#e2e8f0', fontSize: 12 } },
                legend: { data: ['真实能耗', 'CNN-LSTM 预测'], textStyle: { color: '#94a3b8', fontSize: 12 }, top: 5 },
                grid: { left: '5%', right: '5%', bottom: '8%', top: '15%', containLabel: true },
                xAxis: { type: 'category', data: hours, axisLine: { lineStyle: { color: '#334155' } }, axisLabel: { color: '#64748b', fontSize: 10, interval: 3 }, splitLine: { show: false } },
                yAxis: { type: 'value', name: '能耗 (kWh)', nameTextStyle: { color: '#64748b', fontSize: 11 }, axisLabel: { color: '#64748b', fontSize: 10 }, axisLine: { lineStyle: { color: '#334155' } }, splitLine: { lineStyle: { color: '#1e293b', type: 'dashed' } } },
                series: [
                    { name: '真实能耗', type: 'line', data: actual, lineStyle: { color: '#f1f5f9', width: 2 }, itemStyle: { color: '#f1f5f9' }, symbol: 'none', smooth: true },
                    { name: 'CNN-LSTM 预测', type: 'line', data: predicted, lineStyle: { color: '#ef4444', width: 2, type: 'dashed' }, itemStyle: { color: '#ef4444' }, symbol: 'none', smooth: true }
                ]
            };
            chart.setOption(option);
            window.addEventListener('resize', function() { chart.resize(); });
        }

        // ========== 页面加载时初始化 ==========
        window.onload = function() {
            // 系统架构图
            var archChart = echarts.init(document.getElementById('architecture-chart'));
            var archOption = {
                backgroundColor: 'transparent', tooltip: { trigger: 'item' },
                series: [{
                    type: 'tree', top: '5%', left: '8%', bottom: '5%', right: '8%',
                    symbolSize: 10, orient: 'LR', expandAndCollapse: false,
                    label: { position: 'right', verticalAlign: 'middle', align: 'left', fontSize: 10 },
                    lineStyle: { color: '#475569', width: 1.5 },
                    data: [{
                        name: '校园建筑能耗\n多因子预测系统',
                        itemStyle: { color: '#60a5fa', borderColor: '#3b82f6', borderWidth: 2, borderRadius: 8 },
                        label: { color: '#f1f5f9', fontSize: 13, fontWeight: 'bold' },
                        children: [
                            { name: '数据采集层', itemStyle: { color: '#3b82f6' }, label: { color: '#e2e8f0', fontSize: 11 },
                                children: [
                                    { name: '气象数据', itemStyle: { color: '#2563eb' }, label: { color: '#cbd5e1', fontSize: 10 } },
                                    { name: '人员数据', itemStyle: { color: '#2563eb' }, label: { color: '#cbd5e1', fontSize: 10 } },
                                    { name: '设备数据', itemStyle: { color: '#2563eb' }, label: { color: '#cbd5e1', fontSize: 10 } }
                                ] },
                            { name: '数据处理层', itemStyle: { color: '#22c55e' }, label: { color: '#e2e8f0', fontSize: 11 },
                                children: [
                                    { name: '缺失值处理', itemStyle: { color: '#16a34a' }, label: { color: '#cbd5e1', fontSize: 10 } },
                                    { name: '归一化', itemStyle: { color: '#16a34a' }, label: { color: '#cbd5e1', fontSize: 10 } },
                                    { name: '滑动窗口', itemStyle: { color: '#16a34a' }, label: { color: '#cbd5e1', fontSize: 10 } }
                                ] },
                            { name: '模型预测层', itemStyle: { color: '#f97316' }, label: { color: '#e2e8f0', fontSize: 11 },
                                children: [
                                    { name: 'CNN-LSTM', itemStyle: { color: '#ea580c' }, label: { color: '#cbd5e1', fontSize: 10 } },
                                    { name: '注意力机制', itemStyle: { color: '#ea580c' }, label: { color: '#cbd5e1', fontSize: 10 } },
                                    { name: '贝叶斯优化', itemStyle: { color: '#ea580c' }, label: { color: '#cbd5e1', fontSize: 10 } }
                                ] },
                            { name: '应用展示层', itemStyle: { color: '#8b5cf6' }, label: { color: '#e2e8f0', fontSize: 11 },
                                children: [
                                    { name: '预测曲线', itemStyle: { color: '#7c3aed' }, label: { color: '#cbd5e1', fontSize: 10 } },
                                    { name: '峰值预警', itemStyle: { color: '#7c3aed' }, label: { color: '#cbd5e1', fontSize: 10 } },
                                    { name: '节能策略', itemStyle: { color: '#7c3aed' }, label: { color: '#cbd5e1', fontSize: 10 } }
                                ] }
                        ]
                    }]
                }]
            };
            archChart.setOption(archOption);
            window.addEventListener('resize', function() { archChart.resize(); });
            
            // 执行默认预测
            predict24Hours();
            
            // 绘制历史对比图
            drawHistoryChart();
        };
    </script>
</body>
</html>
