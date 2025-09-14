<script setup>
import { ref, onMounted } from 'vue'

const searchInput = ref(null)
const analyzeBtn = ref(null)
const analysisDisplay = ref(null)
const analysisStatus = ref(null)
const frameCarousel = ref(null)
const progressFill = ref(null)
const statusMessage = ref(null)
const extractionDetails = ref(null)
const resultDisplay = ref(null)

const isAnalyzing = ref(false)
const frameIndex = ref(0)
const analysisFrames = ref([])

const startAnalysis = async () => {
  isAnalyzing.value = true
  analysisDisplay.value.style.display = 'block'
  resultDisplay.value.style.display = 'none'
  
  // 显示loading状态
  analysisStatus.value.style.display = 'block'
  analysisStatus.value.classList.remove('hidden')
  
  // 分析流程
  await runAnalysisSteps()
}

const runAnalysisSteps = async () => {
  const steps = [
    { message: '正在加载视频数据...', progress: 5 },
    { message: '初始化多模态AI模型...', progress: 10 },
    { message: '提取关键帧进行分析...', progress: 15 },
    { message: '执行深度学习预处理...', progress: 25 },
    { message: '能源使用模式识别...', progress: 35 },
    { message: '时间序列数据分析...', progress: 45 },
    { message: '空间分布模式分析...', progress: 55 },
    { message: '异常检测和模式识别...', progress: 65 },
    { message: '效率指标计算...', progress: 75 },
    { message: '智能优化建议生成...', progress: 85 },
    { message: '综合评估报告生成...', progress: 95 },
    { message: '✅ 多模态分析完成！', progress: 100 }
  ]

  for (let i = 0; i < steps.length; i++) {
    statusMessage.value.textContent = steps[i].message
    progressFill.value.style.width = steps[i].progress + '%'
    
    // 在步骤3-7显示帧分析
    if (i >= 3 && i < 8) {
      showFrameAnalysis(i - 3)
    }
    
    await sleep(1200)
  }

  // 分析完成，停止loading状态
  isAnalyzing.value = false
  
  // 平滑隐藏loading指示器
  analysisStatus.value.classList.add('hidden')
  
  // 延迟完全隐藏，让过渡动画完成
  setTimeout(() => {
    analysisStatus.value.style.display = 'none'
  }, 500)
  
  showResults()
}

const showFrameAnalysis = (frameIndex) => {
  if (frameIndex >= analysisFrames.value.length) return
  
  const frame = analysisFrames.value[frameIndex]
  
  // 清空轮播容器
  frameCarousel.value.innerHTML = '<div class="analysis-overlay"></div>'
  
  // 创建图片元素
  const img = document.createElement('img')
  img.src = frame.frame
  img.className = 'frame-image active analyzing'
  img.style.width = '100%'
  img.style.height = '100%'
  img.style.objectFit = 'cover'
  
  // 图片加载失败时的处理
  img.onerror = () => {
    console.log('图片加载失败，使用生成的占位图')
    img.src = createEnergyFrame(frame.energy, `分析帧 ${frameIndex + 1}`)
  }
  
  frameCarousel.value.appendChild(img)
  
  // 更新提取信息
  const analysisDetails = getDetailedAnalysisInfo(frameIndex, frame)
  extractionDetails.value.innerHTML = `
    <h4 style="color: #00f5ff; margin-bottom: 10px;">🔍 ${analysisDetails.title}</h4>
    <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 10px; font-size: 13px;">
      <div>⚡ 能源效率: <span style="color: #00ff00">${frame.energy}%</span></div>
      <div>🌡️ 环境温度: <span style="color: #ffff00">${analysisDetails.temp}°C</span></div>
      <div>💡 设备状态: <span style="color: #00ffff">${analysisDetails.devices}</span></div>
      <div>📊 负载系数: <span style="color: #ff00ff">${analysisDetails.load}</span></div>
      <div>🔋 功耗预估: <span style="color: #ffa500">${analysisDetails.power}</span></div>
      <div>🎯 模式识别: <span style="color: #00ff80">${analysisDetails.mode}</span></div>
    </div>
    <div style="margin-top: 10px; padding: 8px; background: rgba(0,255,255,0.1); border-radius: 5px;">
      <div style="font-size: 12px; color: #00ffff;">🔮 AI分析维度: ${analysisDetails.dimension}</div>
      <div style="font-size: 12px; color: #ff00ff;">📈 置信度: ${analysisDetails.confidence}% | 处理: ${analysisDetails.processing}</div>
    </div>
  `
}

const getDetailedAnalysisInfo = (frameIndex, frame) => {
  const analysisTemplates = [
    {
      title: '周一上午能源模式分析 (09:00)',
      temp: (18 + Math.random() * 4).toFixed(1),
      devices: '空调+照明+设备',
      load: '0.85',
      power: '12.4 kWh',
      mode: '办公高效模式',
      dimension: '时间序列 + 空间分布',
      confidence: (88 + Math.random() * 8).toFixed(1),
      processing: '深度卷积分析'
    },
    {
      title: '周二下午负载分析 (15:00)',
      temp: (20 + Math.random() * 3).toFixed(1),
      devices: '空调+部分照明',
      load: '0.72',
      power: '9.8 kWh',
      mode: '标准工作模式',
      dimension: '热力图 + 行为识别',
      confidence: (85 + Math.random() * 10).toFixed(1),
      processing: '多模态融合'
    },
    {
      title: '周三晚间峰值分析 (21:00)',
      temp: (16 + Math.random() * 3).toFixed(1),
      devices: '全部设备运行',
      load: '0.91',
      power: '15.2 kWh',
      mode: '夜间高效模式',
      dimension: '异常检测 + 模式识别',
      confidence: (92 + Math.random() * 6).toFixed(1),
      processing: 'Transformer模型'
    },
    {
      title: '周四中午低效分析 (12:00)',
      temp: (22 + Math.random() * 4).toFixed(1),
      devices: '空调过载+照明',
      load: '0.68',
      power: '8.5 kWh',
      mode: '优化待改进',
      dimension: '能耗异常 + 环境因子',
      confidence: (79 + Math.random() * 12).toFixed(1),
      processing: '决策树分析'
    },
    {
      title: '周五傍晚过渡分析 (18:00)',
      temp: (19 + Math.random() * 3).toFixed(1),
      devices: '渐进式启动',
      load: '0.78',
      power: '11.1 kWh',
      mode: '过渡平衡模式',
      dimension: '时序预测 + 趋势分析',
      confidence: (86 + Math.random() * 9).toFixed(1),
      processing: 'LSTM时序网络'
    }
  ]
  
  return analysisTemplates[frameIndex] || analysisTemplates[0]
}

const createEnergyFrame = (efficiency, timeLabel) => {
  const canvas = document.createElement('canvas')
  canvas.width = 800
  canvas.height = 600
  const ctx = canvas.getContext('2d')
  
  // 背景渐变
  const gradient = ctx.createLinearGradient(0, 0, 800, 600)
  gradient.addColorStop(0, '#001122')
  gradient.addColorStop(1, '#000515')
  ctx.fillStyle = gradient
  ctx.fillRect(0, 0, 800, 600)
  
  // 绘制能源效率可视化
  ctx.strokeStyle = '#00ffff'
  ctx.lineWidth = 3
  ctx.beginPath()
  
  // 绘制效率曲线
  for (let i = 0; i < 24; i++) {
    const x = 50 + (i * 30)
    const baseY = 400
    const variation = Math.sin(i * 0.5) * 50 + (efficiency / 100) * 100
    const y = baseY - variation
    
    if (i === 0) ctx.moveTo(x, y)
    else ctx.lineTo(x, y)
  }
  ctx.stroke()
  
  // 标题
  ctx.fillStyle = '#00ffff'
  ctx.font = 'bold 24px Arial'
  ctx.textAlign = 'center'
  ctx.fillText(`能源效率分析 - ${timeLabel}`, 400, 50)
  
  // 效率值
  ctx.fillStyle = efficiency > 80 ? '#00ff00' : efficiency > 60 ? '#ffff00' : '#ff6600'
  ctx.font = 'bold 48px Arial'
  ctx.fillText(`${efficiency}%`, 400, 150)
  
  // 详细信息
  ctx.fillStyle = '#ffffff'
  ctx.font = '16px Arial'
  ctx.textAlign = 'left'
  ctx.fillText(`时间段: ${timeLabel}`, 50, 500)
  ctx.fillText(`平均功耗: ${(efficiency * 1.2).toFixed(1)} kW`, 50, 530)
  ctx.fillText(`碳排放: ${(100 - efficiency) * 0.5} kg CO₂`, 50, 560)
  
  return canvas.toDataURL()
}

const showResults = () => {
  resultDisplay.value.style.display = 'block'
  resultDisplay.value.innerHTML = `
    <h3 style="color: #ff00ff; margin-bottom: 20px;">📈 一周能源效率多模态分析报告</h3>
    
    <div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); gap: 15px; margin-bottom: 25px;">
      <div style="background: rgba(0,100,255,0.2); padding: 15px; border-radius: 12px; border: 1px solid rgba(0,255,255,0.3);">
        <h4 style="color: #00ffff; font-size: 14px;">📊 综合评分</h4>
        <div style="font-size: 28px; color: #00ff00; font-weight: bold;">78.6%</div>
        <p style="font-size: 11px; color: #ccc;">较上周提升 +3.2%</p>
      </div>
      
      <div style="background: rgba(255,0,100,0.2); padding: 15px; border-radius: 12px; border: 1px solid rgba(255,0,255,0.3);">
        <h4 style="color: #ff00ff; font-size: 14px;">⚡ 节能潜力</h4>
        <div style="font-size: 28px; color: #ffff00; font-weight: bold;">21.4%</div>
        <p style="font-size: 11px; color: #ccc;">预估节省 2.1 kWh/天</p>
      </div>
      
      <div style="background: rgba(0,255,100,0.2); padding: 15px; border-radius: 12px; border: 1px solid rgba(0,255,0,0.3);">
        <h4 style="color: #00ff80; font-size: 14px;">🔋 平均功耗</h4>
        <div style="font-size: 28px; color: #00ff00; font-weight: bold;">11.4kW</div>
        <p style="font-size: 11px; color: #ccc;">峰值: 15.2kW</p>
      </div>
      
      <div style="background: rgba(255,100,0,0.2); padding: 15px; border-radius: 12px; border: 1px solid rgba(255,165,0,0.3);">
        <h4 style="color: #ffa500; font-size: 14px;">🌡️ 环境指数</h4>
        <div style="font-size: 28px; color: #ffff00; font-weight: bold;">19.2°C</div>
        <p style="font-size: 11px; color: #ccc;">适宜温度范围</p>
      </div>
    </div>
    
    <div style="background: linear-gradient(135deg, rgba(100,0,255,0.2), rgba(255,0,100,0.2)); padding: 20px; border-radius: 15px; border: 2px solid rgba(255,100,255,0.4); text-align: center;">
      <h4 style="color: #ff00ff; margin-bottom: 15px;">🚀 AI驱动的未来预期</h4>
      <p style="font-size: 15px; line-height: 1.8; color: #ffffff;">
        通过持续的多模态学习和优化，预计在 <span style="color: #00ffff; font-weight: bold;">3个月内</span> 
        可将整体能源效率提升至 <span style="color: #00ff00; font-weight: bold;">85.2%</span>，
        实现 <span style="color: #ffff00; font-weight: bold;">智能化、绿色化</span> 的能源管理目标。
      </p>
    </div>
  `
}

const generateAnalysisFrames = () => {
  const imageUrls = [
    'st/1.png',
    'st/2.png', 
    'st/3.png',
    'st/4.png',
    'st/5.png'
  ]
  
  analysisFrames.value = [
    { time: '09:00', energy: 85, frame: imageUrls[0] },
    { time: '15:00', energy: 72, frame: imageUrls[1] },
    { time: '21:00', energy: 91, frame: imageUrls[2] },
    { time: '12:00', energy: 68, frame: imageUrls[3] },
    { time: '18:00', energy: 78, frame: imageUrls[4] }
  ]
}

const sleep = (ms) => {
  return new Promise(resolve => setTimeout(resolve, ms))
}

onMounted(() => {
  generateAnalysisFrames()
  analyzeBtn.value.addEventListener('click', startAnalysis)
})
</script>

<template>
  <div class="center-panel">
    <div class="search-container">
      <div class="search-box">
        <input 
          type="text" 
          class="search-input" 
          ref="searchInput"
          placeholder="请评估过去一周能源使用效率？" 
          value="请评估过去一周能源使用效率"
        >
        <button class="analyze-btn" ref="analyzeBtn">🚀 开始分析</button>
      </div>
    </div>

    <div class="analysis-display" ref="analysisDisplay">
      <div class="analysis-status" ref="analysisStatus">
        <div class="status-text">🤖 AI正在进行多模态分析...</div>
        <div class="loading-spinner"></div>
        <div class="progress-bar">
          <div class="progress-fill" ref="progressFill"></div>
        </div>
        <p ref="statusMessage">正在初始化深度学习模型...</p>
      </div>

      <div class="frame-carousel" ref="frameCarousel">
        <div class="analysis-overlay"></div>
      </div>

      <div class="extraction-info">
        <h4 style="color: #00f5ff; margin-bottom: 10px;">🔍 实时信息提取</h4>
        <div ref="extractionDetails">准备开始多维度分析...</div>
      </div>

      <div class="result-display" ref="resultDisplay">
        <h3 style="color: #ff00ff; margin-bottom: 20px;">📈 能源效率评估报告</h3>
        <div id="analysisResults"></div>
      </div>
    </div>
  </div>
</template>

<style scoped>
/* ========== 中间分析区域 ========== */
.center-panel {
  background: rgba(10, 10, 30, 0.95);
  border-radius: 25px;
  padding: 30px;
  border: 2px solid rgba(255, 100, 255, 0.2);
  backdrop-filter: blur(25px);
  position: relative;
  overflow-y: auto;
  overflow-x: hidden;
  height: 100%;
}

/* 自定义滚动条样式 */
.center-panel::-webkit-scrollbar {
  width: 8px;
}

.center-panel::-webkit-scrollbar-track {
  background: rgba(0, 0, 0, 0.3);
  border-radius: 4px;
}

.center-panel::-webkit-scrollbar-thumb {
  background: linear-gradient(45deg, #ff00ff, #00ffff);
  border-radius: 4px;
  transition: all 0.3s ease;
}

.center-panel::-webkit-scrollbar-thumb:hover {
  background: linear-gradient(45deg, #ff0080, #0080ff);
  box-shadow: 0 0 10px rgba(255, 0, 255, 0.5);
}

.center-panel::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: 
    radial-gradient(circle at 20% 80%, rgba(255, 0, 100, 0.1) 0%, transparent 50%),
    radial-gradient(circle at 80% 20%, rgba(0, 255, 100, 0.1) 0%, transparent 50%);
  pointer-events: none;
}

.search-container {
  position: relative;
  margin-bottom: 30px;
  z-index: 10;
}

.search-box {
  position: relative;
  background: rgba(0, 0, 0, 0.6);
  border-radius: 50px;
  padding: 5px;
  border: 2px solid rgba(255, 100, 255, 0.4);
  box-shadow: 0 10px 40px rgba(255, 0, 255, 0.2);
}

.search-input {
  width: calc(100% - 120px);
  background: none;
  border: none;
  padding: 20px 30px;
  color: #fff;
  font-size: 16px;
  outline: none;
}

.search-input::placeholder {
  color: rgba(255, 255, 255, 0.5);
}

.analyze-btn {
  position: absolute;
  right: 5px;
  top: 5px;
  padding: 15px 30px;
  background: linear-gradient(45deg, #ff0080, #8000ff);
  border: none;
  border-radius: 45px;
  color: #fff;
  font-weight: bold;
  cursor: pointer;
  transition: all 0.3s;
}

.analyze-btn:hover {
  transform: scale(1.05);
  box-shadow: 0 5px 25px rgba(255, 0, 128, 0.6);
}

.analysis-display {
  background: rgba(0, 0, 0, 0.8);
  border-radius: 20px;
  padding: 25px;
  min-height: 400px;
  border: 1px solid rgba(255, 100, 255, 0.3);
  position: relative;
  display: none;
  z-index: 10;
}

.analysis-status {
  text-align: center;
  margin-bottom: 20px;
  transition: opacity 0.5s ease, visibility 0.5s ease;
}

.analysis-status.hidden {
  opacity: 0;
  visibility: hidden;
}

.status-text {
  font-size: 20px;
  color: #00f5ff;
  margin-bottom: 10px;
  animation: pulse 2s infinite;
}

@keyframes pulse {
  0%, 100% { opacity: 1; }
  50% { opacity: 0.6; }
}

.loading-spinner {
  width: 50px;
  height: 50px;
  border: 3px solid rgba(255, 255, 255, 0.3);
  border-radius: 50%;
  border-top-color: #00f5ff;
  animation: spin 1s ease-in-out infinite;
  margin: 20px auto;
}

@keyframes spin {
  to { transform: rotate(360deg); }
}

.progress-bar {
  width: 100%;
  height: 8px;
  background: rgba(255, 255, 255, 0.1);
  border-radius: 4px;
  margin: 15px 0;
  overflow: hidden;
}

.progress-fill {
  height: 100%;
  background: linear-gradient(45deg, #00f5ff, #ff00ff);
  width: 0%;
  transition: width 0.3s;
  border-radius: 4px;
  position: relative;
}

.frame-carousel {
  position: relative;
  width: 100%;
  height: 300px;
  border-radius: 15px;
  overflow: hidden;
  margin-bottom: 20px;
  background: #000;
}

.frame-image {
  position: absolute;
  width: 100%;
  height: 100%;
  object-fit: cover;
  opacity: 0;
  transition: all 0.8s ease;
  transform: scale(1);
}

.frame-image.active {
  opacity: 1;
  transform: scale(1.05);
}

.frame-image.analyzing {
  transform: scale(1.15);
  filter: brightness(1.3) contrast(1.2) saturate(1.4);
  box-shadow: 0 0 30px rgba(0, 255, 255, 0.6);
}

.analysis-overlay {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: linear-gradient(45deg, 
    rgba(255, 0, 255, 0.1), 
    rgba(0, 255, 255, 0.1), 
    rgba(255, 255, 0, 0.1));
  opacity: 0;
  animation: analyzeFlash 1.5s infinite;
}

@keyframes analyzeFlash {
  0%, 100% { opacity: 0; }
  50% { opacity: 0.7; }
}

.extraction-info {
  background: rgba(0, 0, 0, 0.9);
  padding: 15px;
  border-radius: 10px;
  border-left: 4px solid #00f5ff;
  margin-top: 15px;
  color: #fff;
}

.result-display {
  background: linear-gradient(135deg, rgba(0, 50, 100, 0.3), rgba(50, 0, 100, 0.3));
  border-radius: 15px;
  padding: 25px;
  margin-top: 20px;
  border: 2px solid rgba(100, 255, 255, 0.4);
  display: none;
  color: #fff;
}

.hidden {
  display: none !important;
}
</style>
