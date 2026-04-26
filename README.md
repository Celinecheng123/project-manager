[project-manager.html](https://github.com/user-attachments/files/27097805/project-manager.html)
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>工程项目全流程管理</title>
  <link rel="stylesheet" href="https://unpkg.com/element-plus@2.4.4/dist/index.css">
  <script src="https://unpkg.com/vue@3.4.15/dist/vue.global.prod.js"></script>
  <script src="https://unpkg.com/element-plus@2.4.4/dist/index.full.min.js"></script>
  <style>
    * { margin: 0; padding: 0; box-sizing: border-box; }
    body { font-family: "Microsoft YaHei", "PingFang SC", sans-serif; background: #f5f7fa; min-height: 100vh; }
    .header { background: linear-gradient(135deg, #1890ff 0%, #0050b3 100%); color: #fff; padding: 20px 30px; }
    .header h1 { font-size: 24px; font-weight: 600; }
    .header p { opacity: 0.9; font-size: 14px; margin-top: 5px; }
    .main-container { max-width: 1400px; margin: 0 auto; padding: 20px; }
    .card { background: #fff; border-radius: 8px; box-shadow: 0 2px 12px rgba(0,0,0,0.08); margin-bottom: 20px; }
    .card-header { padding: 16px 20px; border-bottom: 1px solid #f0f0f0; font-weight: 600; font-size: 16px; display: flex; justify-content: space-between; align-items: center; }
    .card-body { padding: 20px; }
    /* 流程图样式 */
    .flow-container { display: flex; align-items: center; overflow-x: auto; padding: 20px 0; }
    .flow-step { display: flex; flex-direction: column; align-items: center; min-width: 120px; }
    .flow-node { width: 50px; height: 50px; border-radius: 50%; display: flex; align-items: center; justify-content: center; font-size: 20px; font-weight: bold; color: #fff; cursor: pointer; transition: all 0.3s; }
    .flow-node.active { background: #1890ff; transform: scale(1.1); box-shadow: 0 4px 12px rgba(24,144,255,0.4); }
    .flow-node.completed { background: #52c41a; }
    .flow-node.pending { background: #d9d9d9; }
    .flow-node.warning { background: #faad14; }
    .flow-label { margin-top: 10px; font-size: 13px; text-align: center; max-width: 100px; }
    .flow-date { font-size: 11px; color: #999; margin-top: 4px; }
    .flow-line { flex: 1; height: 3px; background: #e8e8e8; min-width: 40px; margin: 0 -5px; margin-top: -25px; }
    .flow-line.completed { background: #52c41a; }
    /* 看板样式 */
    .kanban { display: grid; grid-template-columns: repeat(auto-fit, minmax(300px, 1fr)); gap: 20px; }
    .kanban-item { background: #fff; border-radius: 8px; padding: 16px; border-left: 4px solid #1890ff; box-shadow: 0 2px 8px rgba(0,0,0,0.06); cursor: pointer; transition: all 0.2s; }
    .kanban-item:hover { transform: translateY(-2px); box-shadow: 0 4px 16px rgba(0,0,0,0.1); }
    .kanban-item .title { font-weight: 600; font-size: 15px; margin-bottom: 8px; }
    .kanban-item .info { font-size: 13px; color: #666; margin-bottom: 4px; }
    .kanban-item .progress-bar { height: 6px; background: #f0f0f0; border-radius: 3px; margin-top: 12px; overflow: hidden; }
    .kanban-item .progress-fill { height: 100%; background: #1890ff; border-radius: 3px; transition: width 0.3s; }
    .kanban-item .progress-text { font-size: 12px; color: #999; margin-top: 6px; text-align: right; }
    /* 检查清单样式 */
    .checklist-item { display: flex; align-items: center; padding: 12px; border-bottom: 1px solid #f0f0f0; }
    .checklist-item:last-child { border-bottom: none; }
    .checklist-item .check-icon { width: 24px; height: 24px; border-radius: 50%; display: flex; align-items: center; justify-content: center; margin-right: 12px; cursor: pointer; }
    .checklist-item .check-icon.checked { background: #52c41a; color: #fff; }
    .checklist-item .check-icon.unchecked { border: 2px solid #d9d9d9; }
    .checklist-item .check-text { flex: 1; font-size: 14px; }
    .checklist-item .check-text.done { text-decoration: line-through; color: #999; }
    .checklist-item .check-desc { font-size: 12px; color: #999; margin-left: 36px; }
    .checklist-item .expand-btn { color: #1890ff; font-size: 10px; }
    .sub-checklist-item { display: flex; align-items: center; padding: 8px 0; border-bottom: 1px dashed #f0f0f0; }
    .sub-checklist-item:last-child { border-bottom: none; }
    /* 选址评估表格 */
    .eval-table { width: 100%; }
    .eval-table td { padding: 12px 8px; border-bottom: 1px solid #f0f0f0; vertical-align: top; }
    .eval-table td:first-child { font-weight: 600; width: 120px; color: #333; }
    /* 报告预览 */
    .report-preview { background: #fafafa; padding: 30px; border-radius: 8px; border: 1px solid #e8e8e8; }
    .report-preview h2 { text-align: center; margin-bottom: 20px; }
    .report-preview .report-section { margin-bottom: 24px; }
    .report-preview .report-section h3 { font-size: 16px; color: #1890ff; margin-bottom: 12px; padding-bottom: 8px; border-bottom: 1px dashed #ccc; }
    .report-preview table { width: 100%; border-collapse: collapse; margin-top: 10px; }
    .report-preview table th, .report-preview table td { border: 1px solid #ddd; padding: 10px; text-align: left; font-size: 13px; }
    .report-preview table th { background: #f5f5f5; font-weight: 600; }
    /* 状态标签 */
    .status-tag { padding: 4px 12px; border-radius: 12px; font-size: 12px; }
    .status-tag.pending { background: #f0f0f0; color: #666; }
    .status-tag.active { background: #e6f7ff; color: #1890ff; }
    .status-tag.completed { background: #f6ffed; color: #52c41a; }
    .status-tag.warning { background: #fffbe6; color: #faad14; }
    /* 统计卡片 */
    .stats-grid { display: grid; grid-template-columns: repeat(4, 1fr); gap: 16px; margin-bottom: 20px; }
    .stat-card { background: #fff; border-radius: 8px; padding: 20px; box-shadow: 0 2px 8px rgba(0,0,0,0.06); }
    .stat-card .stat-num { font-size: 32px; font-weight: bold; color: #1890ff; }
    .stat-card .stat-label { font-size: 14px; color: #666; margin-top: 5px; }
    /* 导航tabs */
    .nav-tabs { background: #fff; padding: 0 20px; border-bottom: 1px solid #f0f0f0; display: flex; gap: 30px; }
    .nav-tabs .tab { padding: 16px 0; font-size: 15px; color: #666; cursor: pointer; border-bottom: 2px solid transparent; transition: all 0.2s; }
    .nav-tabs .tab:hover { color: #1890ff; }
    .nav-tabs .tab.active { color: #1890ff; border-bottom-color: #1890ff; font-weight: 600; }
    /* 按钮组 */
    .btn-group { display: flex; gap: 10px; }
    /* 空状态 */
    .empty-state { text-align: center; padding: 60px 20px; color: #999; }
    .empty-state .icon { font-size: 48px; margin-bottom: 16px; }
    /* 模态框内表格 */
    .inline-form .el-form-item { margin-bottom: 16px; }
    /* 响应式 */
    @media (max-width: 768px) {
      .stats-grid { grid-template-columns: repeat(2, 1fr); }
      .kanban { grid-template-columns: 1fr; }
      .header { padding: 15px; }
      .header h1 { font-size: 18px; }
      .main-container { padding: 10px; }
      .nav-tabs { flex-wrap: wrap; gap: 8px; padding: 10px; }
      .nav-tab { padding: 8px 12px; font-size: 13px; white-space: nowrap; }
      .card-header { padding: 12px 15px; font-size: 14px; }
      .card-body { padding: 15px; }
      .modal-content { width: 95%; margin: 10% auto; max-height: 85vh; }
    }
    /* 预警模块 */
    .alert-badge { position: absolute; top: -8px; right: -8px; background: #ff4d4f; color: #fff; border-radius: 50%; width: 20px; height: 20px; font-size: 12px; display: flex; align-items: center; justify-content: center; }
    .alert-section { margin-bottom: 20px; }
    .alert-card { border-radius: 8px; padding: 16px; margin-bottom: 12px; display: flex; align-items: flex-start; gap: 12px; }
    .alert-card.danger { background: #fff2f0; border-left: 4px solid #ff4d4f; }
    .alert-card.warning { background: #fffbe6; border-left: 4px solid #faad14; }
    .alert-icon-lg { font-size: 24px; margin-top: 2px; }
    .alert-info { flex: 1; }
    .alert-project { font-weight: 600; font-size: 14px; margin-bottom: 4px; }
    .alert-stage { font-size: 13px; color: #666; margin-bottom: 4px; }
    .alert-message { font-size: 13px; color: #999; }
    .alert-action { margin-top: 8px; }
    /* 强制提醒弹窗 */
    .alert-modal-overlay { position: fixed; top: 0; left: 0; right: 0; bottom: 0; background: rgba(0,0,0,0.5); z-index: 9999; display: flex; align-items: center; justify-content: center; }
    .alert-modal { background: #fff; border-radius: 12px; width: 90%; max-width: 500px; max-height: 80vh; overflow: hidden; box-shadow: 0 8px 32px rgba(0,0,0,0.2); }
    .alert-modal-header { background: linear-gradient(135deg, #ff4d4f 0%, #ff7875 100%); color: #fff; padding: 16px 20px; font-weight: 600; display: flex; justify-content: space-between; align-items: center; }
    .alert-modal-body { padding: 16px; max-height: 60vh; overflow-y: auto; }
    .alert-item { display: flex; gap: 12px; padding: 16px; border-radius: 8px; margin-bottom: 12px; }
    .alert-item.danger { background: #fff2f0; }
    .alert-item.warning { background: #fffbe6; }
    .alert-item .alert-icon { font-size: 28px; }
    .alert-item .alert-content { flex: 1; }
    .alert-item .alert-title { font-weight: 600; margin-bottom: 4px; }
    .alert-item .alert-desc { font-size: 13px; color: #666; margin-bottom: 8px; }
  </style>
</head>
<body>
  <div id="app">
    <!-- 头部 -->
    <div class="header">
      <h1>🏗️ 工程项目全流程管理系统</h1>
      <p>门店装修项目全周期管理 | 小白项目经理的得力助手</p>
    </div>

    <!-- 导航 -->
    <div class="nav-tabs">
      <div class="tab" :class="{active: currentView === 'dashboard'}" @click="currentView = 'dashboard'">📊 项目看板</div>
      <div class="tab" :class="{active: currentView === 'projects'}" @click="currentView = 'projects'">📋 项目列表</div>
      <div class="tab" :class="{active: currentView === 'evaluate'}" @click="currentView = 'evaluate'">📍 选址评估</div>
      <div class="tab" :class="{active: currentView === 'report'}" @click="currentView = 'report'">📄 报告中心</div>
      <div class="tab" :class="{active: currentView === 'alerts'}" @click="currentView = 'alerts'" style="position:relative;">
        🔔 预警中心
        <span v-if="urgentCount > 0" class="alert-badge">{{ urgentCount }}</span>
      </div>
    </div>

    <div class="main-container">
      <!-- 强制提醒弹窗 -->
      <div v-if="urgentAlerts.length > 0 && showAlertModal" class="alert-modal-overlay" @click.self="showAlertModal = false">
        <div class="alert-modal">
          <div class="alert-modal-header">
            <span>🔔 您有 {{ urgentAlerts.length }} 项节点需要关注</span>
            <el-button link @click="showAlertModal = false">稍后提醒</el-button>
          </div>
          <div class="alert-modal-body">
            <div v-for="alert in urgentAlerts" :key="alert.key" class="alert-item" :class="alert.level">
              <div class="alert-icon">{{ alert.level === 'danger' ? '🔴' : '🟡' }}</div>
              <div class="alert-content">
                <div class="alert-title">{{ alert.projectName }} - {{ alert.stageName }}</div>
                <div class="alert-desc">{{ alert.message }}</div>
                <div class="alert-action">
                  <el-button size="small" type="primary" @click="viewAlertProject(alert)">查看处理</el-button>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- 预警中心 -->
      <div v-if="currentView === 'alerts'">
        <div class="card">
          <div class="card-header">
            <span>🔔 节点预警中心</span>
            <el-button link type="primary" @click="refreshAlerts">🔄 刷新</el-button>
          </div>
          <div class="card-body">
            <!-- 统计 -->
            <div style="display:flex;gap:20px;margin-bottom:20px;">
              <div style="flex:1;background:#fff2f0;padding:16px;border-radius:8px;text-align:center;">
                <div style="font-size:28px;font-weight:bold;color:#ff4d4f;">{{ dangerAlerts.length }}</div>
                <div style="color:#666;font-size:13px;">紧急预警</div>
              </div>
              <div style="flex:1;background:#fffbe6;padding:16px;border-radius:8px;text-align:center;">
                <div style="font-size:28px;font-weight:bold;color:#faad14;">{{ warningAlerts.length }}</div>
                <div style="color:#666;font-size:13px;">即将到期</div>
              </div>
              <div style="flex:1;background:#f6ffed;padding:16px;border-radius:8px;text-align:center;">
                <div style="font-size:28px;font-weight:bold;color:#52c41a;">{{ normalCount }}</div>
                <div style="color:#666;font-size:13px;">正常项目</div>
              </div>
            </div>

            <!-- 紧急预警 -->
            <div v-if="dangerAlerts.length > 0" class="alert-section">
              <h4 style="color:#ff4d4f;margin-bottom:12px;">🔴 紧急预警（已逾期/今日到期）</h4>
              <div v-for="alert in dangerAlerts" :key="alert.key" class="alert-card danger">
                <div class="alert-icon-lg">🔴</div>
                <div class="alert-info">
                  <div class="alert-project">{{ alert.projectName }}</div>
                  <div class="alert-stage">📍 {{ alert.stageName }} | {{ alert.projectAddress }}</div>
                  <div class="alert-message">{{ alert.message }}</div>
                  <div class="alert-action">
                    <el-button type="danger" size="small" @click="viewAlertProject(alert)">立即处理</el-button>
                  </div>
                </div>
              </div>
            </div>

            <!-- 即将到期 -->
            <div v-if="warningAlerts.length > 0" class="alert-section">
              <h4 style="color:#faad14;margin-bottom:12px;">🟡 即将到期（3天内）</h4>
              <div v-for="alert in warningAlerts" :key="alert.key" class="alert-card warning">
                <div class="alert-icon-lg">🟡</div>
                <div class="alert-info">
                  <div class="alert-project">{{ alert.projectName }}</div>
                  <div class="alert-stage">📍 {{ alert.stageName }} | {{ alert.projectAddress }}</div>
                  <div class="alert-message">{{ alert.message }}</div>
                  <div class="alert-action">
                    <el-button type="warning" size="small" plain @click="viewAlertProject(alert)">查看详情</el-button>
                  </div>
                </div>
              </div>
            </div>

            <!-- 无预警 -->
            <div v-if="urgentAlerts.length === 0" style="text-align:center;padding:60px;color:#52c41a;">
              <div style="font-size:48px;">✅</div>
              <div style="font-size:18px;margin-top:16px;">太棒了！暂无预警项目</div>
              <div style="color:#999;margin-top:8px;">所有项目节点状态正常，继续保持</div>
            </div>
          </div>
        </div>
      </div>

      <!-- 项目看板 -->
      <div v-if="currentView === 'dashboard'">
        <!-- 紧急预警横幅 -->
        <div v-if="dangerAlerts.length > 0" class="card" style="background:#fff2f0;border:2px solid #ff4d4f;">
          <div class="card-body" style="display:flex;align-items:center;gap:16px;">
            <div style="font-size:36px;">🚨</div>
            <div style="flex:1;">
              <div style="font-weight:600;color:#ff4d4f;font-size:16px;">您有 {{ dangerAlerts.length }} 项紧急预警</div>
              <div style="color:#666;font-size:13px;margin-top:4px;">
                {{ dangerAlerts.map(a => a.projectName + '-' + a.stageName).join('、') }}
              </div>
            </div>
            <el-button type="danger" @click="currentView = 'alerts'">立即查看</el-button>
          </div>
        </div>

        <!-- 统计卡片 -->
        <div class="stats-grid">
          <div class="stat-card">
            <div class="stat-num">{{ projects.length }}</div>
            <div class="stat-label">总项目数</div>
          </div>
          <div class="stat-card">
            <div class="stat-num">{{ activeProjects }}</div>
            <div class="stat-label">进行中</div>
          </div>
          <div class="stat-card">
            <div class="stat-num">{{ completedProjects }}</div>
            <div class="stat-label">已完成</div>
          </div>
          <div class="stat-card">
            <div class="stat-num">{{ overdueProjects }}</div>
            <div class="stat-label">逾期预警</div>
          </div>
        </div>

        <!-- 流程概览 -->
        <div class="card">
          <div class="card-header">
            <span>📈 项目整体流程进度</span>
          </div>
          <div class="card-body">
            <div class="flow-container">
              <div v-for="(stage, index) in stages" :key="index" style="display:flex;align-items:center;">
                <div class="flow-step">
                  <div class="flow-node" :class="getStageClass(stage)">{{ index + 1 }}</div>
                  <div class="flow-label">{{ stage.name }}</div>
                  <div class="flow-date">{{ stage.count }} 个项目</div>
                </div>
                <div v-if="index < stages.length - 1" class="flow-line" :class="{completed: stage.status === 'completed'}"></div>
              </div>
            </div>
          </div>
        </div>

        <!-- 快速入口 -->
        <div class="card">
          <div class="card-header">
            <span>🚀 快速操作</span>
          </div>
          <div class="card-body">
            <div class="btn-group">
              <el-button type="primary" @click="showAddProject = true">➕ 新建项目</el-button>
              <el-button type="success" @click="currentView = 'evaluate'; resetEvalForm()">📍 选址评估</el-button>
              <el-button type="warning" @click="currentView = 'report'">📄 生成报告</el-button>
            </div>
          </div>
        </div>

        <!-- 近期项目卡片 -->
        <div class="card">
          <div class="card-header">
            <span>📌 近期项目</span>
            <el-button link type="primary" @click="currentView = 'projects'">查看全部 →</el-button>
          </div>
          <div class="card-body">
            <div class="kanban" v-if="projects.length > 0">
              <div class="kanban-item" v-for="p in recentProjects" :key="p.id" @click="viewProject(p)">
                <div style="display:flex;justify-content:space-between;align-items:start;margin-bottom:8px;">
                  <div class="title">{{ p.name }}</div>
                  <span class="status-tag" :class="getStatusClass(p)">{{ getStatusText(p) }}</span>
                </div>
                <div class="info">📍 {{ p.address }}</div>
                <div class="info">📅 {{ p.startDate }} ~ {{ p.endDate }}</div>
                <div class="progress-bar">
                  <div class="progress-fill" :style="{width: getProgress(p) + '%', background: getProgressColor(p)}"></div>
                </div>
                <div class="progress-text">{{ getProgress(p) }}% 完成</div>
              </div>
            </div>
            <div v-else class="empty-state">
              <div class="icon">📦</div>
              <p>暂无项目，点击上方"新建项目"开始</p>
            </div>
          </div>
        </div>
      </div>

      <!-- 项目列表 -->
      <div v-if="currentView === 'projects'">
        <div class="card">
          <div class="card-header">
            <span>📋 所有项目</span>
            <div class="btn-group">
              <el-input v-model="searchKeyword" placeholder="搜索项目名称" style="width:200px;" clearable></el-input>
              <el-button type="primary" @click="showAddProject = true">➕ 新建项目</el-button>
            </div>
          </div>
          <div class="card-body">
            <el-table :data="filteredProjects" stripe style="width:100%">
              <el-table-column prop="name" label="项目名称" min-width="150"></el-table-column>
              <el-table-column prop="address" label="地址" min-width="150"></el-table-column>
              <el-table-column prop="stage" label="当前阶段" width="120">
                <template #default="{row}">
                  <span class="status-tag" :class="getStageStatusClass(row)">{{ getStageName(row) }}</span>
                </template>
              </el-table-column>
              <el-table-column prop="progress" label="进度" width="150">
                <template #default="{row}">
                  <el-progress :percentage="getProgress(row)" :color="getProgressColor(row)"></el-progress>
                </template>
              </el-table-column>
              <el-table-column prop="endDate" label="计划完成" width="110"></el-table-column>
              <el-table-column label="操作" width="180" fixed="right">
                <template #default="{row}">
                  <el-button link type="primary" @click="viewProject(row)">查看</el-button>
                  <el-button link type="warning" @click="editProject(row)">编辑</el-button>
                  <el-button link type="danger" @click="deleteProject(row)">删除</el-button>
                </template>
              </el-table-column>
            </el-table>
          </div>
        </div>
      </div>

      <!-- 选址评估 -->
      <div v-if="currentView === 'evaluate'">
        <div class="card">
          <div class="card-header">
            <span>📍 门店选址评估表</span>
            <span style="font-size:12px;color:#999;">填写完成后可一键转为项目</span>
          </div>
          <div class="card-body">
            <table class="eval-table">
              <tr>
                <td>场地名称<span style="color:#ff4d4f">*</span></td>
                <td><el-input v-model="evalForm.name" placeholder="如：天河城旗舰店"></el-input></td>
              </tr>
              <tr>
                <td>详细地址<span style="color:#ff4d4f">*</span></td>
                <td><el-input v-model="evalForm.address" placeholder="省市区街道门牌号"></el-input></td>
              </tr>
              <tr>
                <td>面积（㎡）<span style="color:#ff4d4f">*</span></td>
                <td><el-input-number v-model="evalForm.area" :min="0" :step="10"></el-input-number></td>
              </tr>
              <tr>
                <td>租金（元/月）<span style="color:#ff4d4f">*</span></td>
                <td><el-input-number v-model="evalForm.rent" :min="0" :step="1000"></el-input-number></td>
              </tr>
              <tr>
                <td>单价（元/㎡/月）</td>
                <td><span style="color:#1890ff;font-weight:bold;">{{ evalForm.area > 0 ? (evalForm.rent / evalForm.area).toFixed(2) : 0 }}</span></td>
              </tr>
              <tr>
                <td>押金倍数</td>
                <td>
                  <el-select v-model="evalForm.deposit" style="width:200px;">
                    <el-option label="1个月" :value="1"></el-option>
                    <el-option label="2个月" :value="2"></el-option>
                    <el-option label="3个月" :value="3"></el-option>
                    <el-option label="6个月" :value="6"></el-option>
                  </el-select>
                </td>
              </tr>
              <tr>
                <td>免租期（天）</td>
                <td><el-input-number v-model="evalForm.freeDays" :min="0" :max="180"></el-input-number></td>
              </tr>
              <tr>
                <td>楼层</td>
                <td><el-input v-model="evalForm.floor" placeholder="如：1楼/负1楼"></el-input></td>
              </tr>
              <tr>
                <td>层高（米）</td>
                <td><el-input-number v-model="evalForm.height" :min="0" :precision="1" :step="0.1"></el-input-number></td>
              </tr>
              <tr>
                <td>承重（kg/㎡）</td>
                <td><el-input-number v-model="evalForm.load" :min="0" :precision="0"></el-input-number></td>
              </tr>
              <tr>
                <td>电力容量（kW）</td>
                <td><el-input-number v-model="evalForm.power" :min="0"></el-input-number></td>
              </tr>
              <tr>
                <td>消防验收</td>
                <td>
                  <el-radio-group v-model="evalForm.fireApproval">
                    <el-radio label="已通过">已通过</el-radio>
                    <el-radio label="待验收">待验收</el-radio>
                    <el-radio label="无">无</el-radio>
                  </el-radio-group>
                </td>
              </tr>
              <tr>
                <td>产权性质</td>
                <td>
                  <el-select v-model="evalForm.propertyType" placeholder="请选择">
                    <el-option label="国有出让" value="国有出让"></el-option>
                    <el-option label="集体土地" value="集体土地"></el-option>
                    <el-option label="军队用地" value="军队用地"></el-option>
                    <el-option label="其他" value="其他"></el-option>
                  </el-select>
                </td>
              </tr>
              <tr>
                <td>周边人流（高/中/低）</td>
                <td>
                  <el-rate v-model="evalForm.footTraffic" :max="3"></el-rate>
                  <span style="margin-left:10px;color:#666;">{{ ['','低','中','高'][evalForm.footTraffic] || '未评分' }}</span>
                </td>
              </tr>
              <tr>
                <td>竞争店数量</td>
                <td><el-input-number v-model="evalForm.competitors" :min="0"></el-input-number></td>
              </tr>
              <tr>
                <td>交通便利性</td>
                <td>
                  <el-radio-group v-model="evalForm.traffic">
                    <el-radio label="优">优</el-radio>
                    <el-radio label="良">良</el-radio>
                    <el-radio label="一般">一般</el-radio>
                    <el-radio label="差">差</el-radio>
                  </el-radio-group>
                </td>
              </tr>
              <tr>
                <td>停车位</td>
                <td>
                  <el-input-number v-model="evalForm.parking" :min="0"></el-input-number> 个
                </td>
              </tr>
              <tr>
                <td>装修限制</td>
                <td>
                  <el-checkbox-group v-model="evalForm.restrictions">
                    <el-checkbox label="无限制">无限制</el-checkbox>
                    <el-checkbox label="营业时间限制">营业时间限制</el-checkbox>
                    <el-checkbox label="油烟排放限制">油烟排放限制</el-checkbox>
                    <el-checkbox label="噪音限制">噪音限制</el-checkbox>
                    <el-checkbox label="需要物业审批">需要物业审批</el-checkbox>
                  </el-checkbox-group>
                </td>
              </tr>
              <tr>
                <td>中介费（元）</td>
                <td><el-input-number v-model="evalForm.agentFee" :min="0"></el-input-number></td>
              </tr>
              <tr>
                <td>转让费（元）</td>
                <td><el-input-number v-model="evalForm.transferFee" :min="0"></el-input-number></td>
              </tr>
              <tr>
                <td>备注</td>
                <td><el-input type="textarea" v-model="evalForm.remark" :rows="3" placeholder="其他补充说明"></el-input></td>
              </tr>
              <tr>
                <td>综合评分</td>
                <td>
                  <div style="display:flex;align-items:center;gap:16px;">
                    <el-input-number v-model="evalForm.score" :min="0" :max="100" :disabled="true"></el-input-number>
                    <span style="font-size:12px;color:#999;">（系统根据各项指标自动计算）</span>
                  </div>
                </td>
              </tr>
            </table>
            <div style="margin-top:24px;display:flex;gap:12px;">
              <el-button type="primary" @click="generateEvalScore">📊 计算评分</el-button>
              <el-button type="success" @click="convertToProject">✅ 转为项目</el-button>
              <el-button @click="resetEvalForm">🔄 重置</el-button>
            </div>
          </div>
        </div>

        <!-- 历史评估记录 -->
        <div class="card" v-if="evaluationRecords.length > 0">
          <div class="card-header">
            <span>📝 历史评估记录</span>
          </div>
          <div class="card-body">
            <el-table :data="evaluationRecords" stripe>
              <el-table-column prop="name" label="场地名称" min-width="150"></el-table-column>
              <el-table-column prop="address" label="地址" min-width="180"></el-table-column>
              <el-table-column prop="area" label="面积(㎡)" width="90"></el-table-column>
              <el-table-column prop="rent" label="租金" width="100">
                <template #default="{row}">¥{{ row.rent?.toLocaleString() }}</template>
              </el-table-column>
              <el-table-column prop="score" label="评分" width="80">
                <template #default="{row}">
                  <span :style="{color: row.score >= 70 ? '#52c41a' : row.score >= 50 ? '#faad14' : '#ff4d4f', fontWeight: 'bold'}">{{ row.score }}</span>
                </template>
              </el-table-column>
              <el-table-column prop="date" label="评估日期" width="110"></el-table-column>
              <el-table-column label="操作" width="120">
                <template #default="{row}">
                  <el-button link type="primary" @click="loadEvalRecord(row)">查看详情</el-button>
                </template>
              </el-table-column>
            </el-table>
          </div>
        </div>
      </div>

      <!-- 报告中心 -->
      <div v-if="currentView === 'report'">
        <div class="card">
          <div class="card-header">
            <span>📄 报告生成中心</span>
          </div>
          <div class="card-body">
            <el-form inline>
              <el-form-item label="选择项目">
                <el-select v-model="reportProjectId" placeholder="请选择项目" style="width:300px;" @change="generateReport">
                  <el-option v-for="p in projects" :key="p.id" :label="p.name" :value="p.id"></el-option>
                </el-select>
              </el-form-item>
              <el-form-item label="报告类型">
                <el-select v-model="reportType" style="width:150px;">
                  <el-option label="项目周报" value="weekly"></el-option>
                  <el-option label="项目月报" value="monthly"></el-option>
                  <el-option label="项目总报告" value="summary"></el-option>
                </el-select>
              </el-form-item>
            </el-form>

            <div v-if="reportData" class="report-preview">
              <h2>{{ reportData.title }}</h2>
              <p style="text-align:center;color:#999;margin-bottom:30px;">生成时间：{{ reportData.generatedAt }}</p>

              <div class="report-section">
                <h3>一、项目基本信息</h3>
                <table>
                  <tr><th style="width:150px;">项目名称</th><td>{{ reportData.basic.name }}</td></tr>
                  <tr><th>项目地址</th><td>{{ reportData.basic.address }}</td></tr>
                  <tr><th>当前阶段</th><td>{{ reportData.basic.stage }}</td></tr>
                  <tr><th>项目进度</th><td>{{ reportData.basic.progress }}%</td></tr>
                  <tr><th>计划周期</th><td>{{ reportData.basic.period }}</td></tr>
                  <tr><th>项目经理</th><td>{{ reportData.basic.manager }}</td></tr>
                </table>
              </div>

              <div class="report-section">
                <h3>二、各阶段完成情况</h3>
                <table>
                  <tr>
                    <th>阶段</th>
                    <th>状态</th>
                    <th>开始时间</th>
                    <th>完成时间</th>
                    <th>检查项完成率</th>
                  </tr>
                  <tr v-for="s in reportData.stages" :key="s.name">
                    <td>{{ s.name }}</td>
                    <td><span class="status-tag" :class="s.statusClass">{{ s.status }}</span></td>
                    <td>{{ s.startDate || '-' }}</td>
                    <td>{{ s.endDate || '-' }}</td>
                    <td>{{ s.checkRate }}%</td>
                  </tr>
                </table>
              </div>

              <div class="report-section">
                <h3>三、下阶段工作计划</h3>
                <p>{{ reportData.nextPlan }}</p>
              </div>

              <div class="report-section">
                <h3>四、风险提示</h3>
                <ul style="padding-left:20px;">
                  <li v-for="r in reportData.risks" :key="r">{{ r }}</li>
                </ul>
              </div>

              <div class="report-section">
                <h3>五、经费使用情况</h3>
                <table>
                  <tr><th style="width:150px;">预算总额</th><td>¥{{ reportData.budget.total?.toLocaleString() }}</td></tr>
                  <tr><th>已使用</th><td>¥{{ reportData.budget.used?.toLocaleString() }}</td></tr>
                  <tr><th>剩余预算</th><td>¥{{ (reportData.budget.total - reportData.budget.used)?.toLocaleString() }}</td></tr>
                </table>
              </div>
            </div>

            <div v-else class="empty-state">
              <div class="icon">📊</div>
              <p>请选择项目，系统将自动生成项目报告</p>
            </div>

            <div v-if="reportData" style="margin-top:20px;">
              <el-button type="primary" @click="exportReport">📥 导出报告</el-button>
            </div>
          </div>
        </div>
      </div>

      <!-- 新建/编辑项目弹窗 -->
      <el-dialog v-model="showAddProject" :title="editingProject ? '编辑项目' : '新建项目'" width="600px">
        <el-form :model="projectForm" label-width="100px" class="inline-form">
          <el-form-item label="项目名称" required>
            <el-input v-model="projectForm.name" placeholder="如：广州天河城店装修"></el-input>
          </el-form-item>
          <el-form-item label="项目地址" required>
            <el-input v-model="projectForm.address" placeholder="详细地址"></el-input>
          </el-form-item>
          <el-form-item label="项目经理">
            <el-input v-model="projectForm.manager"></el-input>
          </el-form-item>
          <el-form-item label="计划开始">
            <el-date-picker v-model="projectForm.startDate" type="date" style="width:100%;"></el-date-picker>
          </el-form-item>
          <el-form-item label="计划完成">
            <el-date-picker v-model="projectForm.endDate" type="date" style="width:100%;"></el-date-picker>
          </el-form-item>
          <el-form-item label="预算总额">
            <el-input-number v-model="projectForm.budget" :min="0" :step="10000"></el-input-number>
          </el-form-item>
          <el-form-item label="备注">
            <el-input type="textarea" v-model="projectForm.remark" :rows="3"></el-input>
          </el-form-item>
        </el-form>
        <template #footer>
          <el-button @click="showAddProject = false">取消</el-button>
          <el-button type="primary" @click="saveProject">保存</el-button>
        </template>
      </el-dialog>

      <!-- 项目详情弹窗 -->
      <el-dialog v-model="showProjectDetail" title="项目详情" width="900px">
        <div v-if="currentProject">
          <div style="display:flex;justify-content:space-between;align-items:center;margin-bottom:20px;">
            <div>
              <h2 style="margin-bottom:5px;">{{ currentProject.name }}</h2>
              <span class="status-tag" :class="getStageStatusClass(currentProject)">{{ getStageName(currentProject) }}</span>
            </div>
            <el-progress :percentage="getProgress(currentProject)" :color="getProgressColor(currentProject)" style="width:200px;"></el-progress>
          </div>

          <!-- 项目流程 -->
          <div class="flow-container" style="margin-bottom:24px;">
            <div v-for="(stage, index) in stages" :key="index" style="display:flex;align-items:center;">
              <div class="flow-step">
                <div class="flow-node" :class="getProjectStageClass(currentProject, index)" @click="setProjectStage(index)">{{ index + 1 }}</div>
                <div class="flow-label">{{ stage.name }}</div>
                <div class="flow-date" v-if="getStageDate(currentProject, index)">{{ getStageDate(currentProject, index) }}</div>
              </div>
              <div v-if="index < stages.length - 1" class="flow-line" :class="{completed: currentProject.stageIndex > index}"></div>
            </div>
          </div>

          <!-- 检查清单 -->
          <div style="margin-bottom:20px;">
            <div style="display:flex;justify-content:space-between;align-items:center;margin-bottom:12px;">
              <h3 style="font-size:15px;">✅ {{ stages[currentProject.stageIndex]?.name }} - 检查清单</h3>
              <span style="font-size:13px;color:#999;">{{ getCompletedChecklist(currentProject) }}/{{ getTotalChecklist(currentProject) }} 已完成</span>
            </div>
            <div class="card" style="box-shadow:none;border:1px solid #f0f0f0;">
              <div v-for="(item, idx) in getCurrentChecklist(currentProject)" :key="idx">
                <!-- 主检查项 -->
                <div class="checklist-item">
                  <div v-if="item.hasSub" class="expand-btn" @click="toggleExpand(currentProject, currentProject.stageIndex, idx)" style="width:24px;height:24px;display:flex;align-items:center;justify-content:center;margin-right:8px;cursor:pointer;color:#1890ff;font-size:12px;">
                    <span v-if="item.expanded">▼</span>
                    <span v-else>▶</span>
                  </div>
                  <div v-else style="width:24px;margin-right:8px;"></div>
                  <div class="check-icon" :class="item.done ? 'checked' : 'unchecked'" @click="toggleChecklist(currentProject, currentProject.stageIndex, idx)">
                    <span v-if="item.done">✓</span>
                  </div>
                  <div style="flex:1;">
                    <div class="check-text" :class="{done: item.done}">{{ item.text }}</div>
                    <div class="check-desc">{{ item.desc }}</div>
                    <div v-if="item.hasSub && item.expanded" style="margin-top:8px;padding-left:16px;">
                      <div v-for="(sub, subIdx) in item.subItems" :key="subIdx" class="sub-checklist-item">
                        <div class="check-icon" :class="sub.done ? 'checked' : 'unchecked'" @click.stop="toggleSubItem(currentProject, currentProject.stageIndex, idx, subIdx)" style="width:20px;height:20px;font-size:12px;">
                          <span v-if="sub.done">✓</span>
                        </div>
                        <div style="flex:1;">
                          <div class="check-text" :class="{done: sub.done}" style="font-size:13px;">{{ sub.text }}</div>
                        </div>
                      </div>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>

          <!-- 项目日志 -->
          <div>
            <h3 style="font-size:15px;margin-bottom:12px;">📝 项目日志</h3>
            <el-timeline>
              <el-timeline-item v-for="log in currentProject.logs" :key="log.id" :timestamp="log.date" placement="top">
                {{ log.content }}
              </el-timeline-item>
            </el-timeline>
            <div style="margin-top:16px;display:flex;gap:10px;">
              <el-input v-model="newLogContent" placeholder="添加日志记录..." style="flex:1;"></el-input>
              <el-button type="primary" @click="addLog">添加</el-button>
            </div>
          </div>
        </div>
        <template #footer>
          <el-button @click="showProjectDetail = false">关闭</el-button>
        </template>
      </el-dialog>
    </div>
  </div>

  <script>
    const { createApp, ref, computed, watch, onMounted } = Vue;

    const app = createApp({
      setup() {
        // 项目阶段配置
        const stages = [
          { name: '选址评估', status: 'pending' },
          { name: '项目立项', status: 'pending' },
          { name: '合同签订', status: 'pending' },
          { name: '设计阶段', status: 'pending' },
          { name: '施工准备', status: 'pending' },
          { name: '施工过程', status: 'pending' },
          { name: '竣工验收', status: 'pending' },
          { name: '交付维保', status: 'pending' }
        ];

        // 各阶段检查清单（支持子项）
        const checklists = {
          0: [ // 选址评估
            { text: '场地实地考察', desc: '拍摄现场照片，评估现有条件', done: false },
            { text: '产权证明核查', desc: '房产证/租赁合同原件', done: false },
            { text: '消防条件评估', desc: '确认消防验收是否通过', done: false, hasSub: true, expanded: false, subItems: [
              { text: '消防栓位置是否影响布局规划', done: false },
              { text: '逃生通道宽度≥1.2m', done: false },
              { text: '消防喷淋头高度是否影响吊顶', done: false },
              { text: '烟雾探测器位置是否合理', done: false },
              { text: '消防应急灯配置完整', done: false },
              { text: '防火门配置及状态', done: false },
              { text: '消防疏散指示标识', done: false },
              { text: '消防验收合格证有效期', done: false }
            ]},
            { text: '电力容量确认', desc: '是否满足装修及运营需求', done: false },
            { text: '周边竞争分析', desc: '半径500米内同类门店数量', done: false },
            { text: '租金成本测算', desc: '月租金+物业费+杂费', done: false },
            { text: '投资回报测算', desc: '预估回本周期', done: false },
            { text: '选址评估报告', desc: '完成书面评估报告', done: false }
          ],
          1: [ // 项目立项
            { text: '项目立项申请', desc: '提交立项审批流程', done: false },
            { text: '预算编制', desc: '装修+设备+运营预备金', done: false },
            { text: '项目负责人指派', desc: '明确项目经理', done: false },
            { text: '里程碑计划制定', desc: '各阶段关键节点', done: false },
            { text: '立项会议召开', desc: '相关部门参与评审', done: false }
          ],
          2: [ // 合同签订
            { text: '施工合同起草', desc: '明确施工范围、工期、付款方式', done: false },
            { text: '设计合同签订', desc: '含设计费、版权归属', done: false },
            { text: '造价清单审核', desc: '预算清单逐项核对', done: false },
            { text: '付款条款谈判', desc: '首付款比例、进度款节点', done: false },
            { text: '合同审批流程', desc: '法务+财务+管理层审批', done: false },
            { text: '合同盖章归档', desc: '双方签字盖章原件留存', done: false }
          ],
          3: [ // 设计阶段
            { text: '需求调研', desc: '收集使用部门需求', done: false },
            { text: '平面方案设计', desc: '动线规划、功能分区', done: false },
            { text: '效果图制作', desc: '3D效果图确认', done: false },
            { text: '施工图设计', desc: '水电点位、隔墙、天花', done: false },
            { text: '材料清单编制', desc: '规格型号参考价格', done: false },
            { text: '设计方案评审', desc: '内部评审会议', done: false },
            { text: '材料样品确认', desc: '打样或实物确认', done: false }
          ],
          4: [ // 施工准备
            { text: '施工许可证办理', desc: '物业+街道报批', done: false },
            { text: '施工队进场交接', desc: '现场条件移交', done: false },
            { text: '图纸会审', desc: '设计方+施工方+管理方', done: false },
            { text: '材料采购下单', desc: '定制类材料优先', done: false },
            { text: '临时设施搭建', desc: '围挡、施工通道', done: false },
            { text: '保险购买', desc: '工程险/第三方责任险', done: false }
          ],
          5: [ // 施工过程
            { text: '拆旧工程', desc: '原有装修拆除', done: false },
            { text: '水电隐蔽工程', desc: '验收合格后方可封闭', done: false, hasSub: true, expanded: false, subItems: [
              { text: '给水管路打压测试（0.6MPa，30分钟无渗漏）', done: false },
              { text: '排水管路灌水测试（无堵塞、无渗漏）', done: false },
              { text: '电线穿管（线径符合设计要求）', done: false },
              { text: '插座/开关底盒安装水平', done: false },
              { text: '强弱电分管穿线（间距≥30cm）', done: false },
              { text: '接地保护安装', done: false },
              { text: '隐蔽工程拍照留档', done: false },
              { text: '监理/业主验收签字确认', done: false }
            ]},
            { text: '空调消防安装', desc: '配合进度', done: false },
            { text: '隔墙隔断工程', desc: '按图纸施工', done: false },
            { text: '天花吊顶工程', desc: '封板前检查管线', done: false },
            { text: '墙面地面工程', desc: '油漆/瓷砖/地板', done: false },
            { text: '门窗安装', desc: '含消防通道门', done: false },
            { text: '设备安装调试', desc: '空调、厨房设备等', done: false },
            { text: '装修清洁', desc: '开荒保洁', done: false },
            { text: '分阶段验收', desc: '每阶段完成签字确认', done: false }
          ],
          6: [ // 竣工验收
            { text: '装修竣工验收', desc: '施工方+管理方联合验收', done: false, hasSub: true, expanded: false, subItems: [
              { text: '地面：平整、无空鼓、无色差', done: false },
              { text: '墙面：垂直、无裂缝、涂料均匀', done: false },
              { text: '天花：无裂缝、灯具安装牢固', done: false },
              { text: '门窗：开关灵活、缝隙均匀', done: false },
              { text: '卫生间：防水测试48小时、无渗漏', done: false },
              { text: '强弱电：通电测试、漏电保护正常', done: false },
              { text: '排水：畅通、无堵塞、无异味', done: false },
              { text: '空调：制冷/制热正常、噪音达标', done: false },
              { text: '消防：烟感/喷淋/应急灯功能正常', done: false },
              { text: '验收签字确认单', done: false }
            ]},
            { text: '消防验收申报', desc: '如有需要', done: false },
            { text: '环保检测', desc: '空气质量检测', done: false },
            { text: '竣工结算', desc: '实际工程量核对', done: false },
            { text: '竣工图纸归档', desc: '竣工图绘制', done: false },
            { text: '尾款支付', desc: '验收合格后支付', done: false }
          ],
          7: [ // 交付维保
            { text: '设备移交培训', desc: '操作培训手册', done: false },
            { text: '钥匙及门禁移交', desc: '全部钥匙清单', done: false },
            { text: '资料档案移交', desc: '合同/图纸/保修卡', done: false },
            { text: '开业前检查', desc: '全面检查清单', done: false },
            { text: '质保金预留', desc: '约定质保期及金额', done: false },
            { text: '维保协议签订', desc: '设备维保约定', done: false },
            { text: '项目复盘总结', desc: '经验教训总结', done: false }
          ]
        };

        // 数据
        const projects = ref([]);
        const evaluationRecords = ref([]);
        const currentView = ref('dashboard');
        const searchKeyword = ref('');
        const showAddProject = ref(false);
        const showProjectDetail = ref(false);
        const editingProject = ref(null);
        const currentProject = ref(null);
        const showAlertModal = ref(false);
        const newLogContent = ref('');

        // 项目表单
        const projectForm = ref({
          name: '',
          address: '',
          manager: '',
          startDate: '',
          endDate: '',
          budget: 0,
          remark: ''
        });

        // 评估表单
        const evalForm = ref({
          name: '',
          address: '',
          area: 0,
          rent: 0,
          deposit: 2,
          freeDays: 0,
          floor: '',
          height: 0,
          load: 0,
          power: 0,
          fireApproval: '已通过',
          propertyType: '国有出让',
          footTraffic: 0,
          competitors: 0,
          traffic: '良',
          parking: 0,
          restrictions: [],
          agentFee: 0,
          transferFee: 0,
          remark: '',
          score: 0
        });

        // 报告相关
        const reportProjectId = ref('');
        const reportType = ref('weekly');
        const reportData = ref(null);

        // 计算属性
        const activeProjects = computed(() => projects.value.filter(p => p.stageIndex > 0 && p.stageIndex < 7).length);
        const completedProjects = computed(() => projects.value.filter(p => p.stageIndex >= 7).length);
        const overdueProjects = computed(() => {
          const now = new Date();
          return projects.value.filter(p => {
            if (!p.endDate || p.stageIndex >= 7) return false;
            return new Date(p.endDate) < now;
          }).length;
        });

        const recentProjects = computed(() => {
          return projects.value.slice(0, 6);
        });

        // 节点预警计算
        const urgentAlerts = computed(() => {
          const alerts = [];
          const today = new Date();
          today.setHours(0, 0, 0, 0);
          const threeDaysLater = new Date(today);
          threeDaysLater.setDate(threeDaysLater.getDate() + 3);

          projects.value.forEach(project => {
            if (project.stageIndex >= 7) return; // 已完成项目不预警

            const stage = stages[project.stageIndex];
            const stageData = project.stageData[project.stageIndex] || {};

            // 检查项目整体截止日期
            if (project.endDate) {
              const endDate = new Date(project.endDate);
              if (endDate < today) {
                alerts.push({
                  key: `${project.id}_overdue`,
                  level: 'danger',
                  projectName: project.name,
                  projectAddress: project.address,
                  stageName: stage?.name || '未知阶段',
                  message: `项目整体已逾期 ${Math.ceil((today - endDate) / (1000 * 60 * 60 * 24))} 天`,
                  projectId: project.id,
                  type: 'overdue'
                });
              } else if (endDate <= threeDaysLater) {
                alerts.push({
                  key: `${project.id}_endSoon`,
                  level: 'warning',
                  projectName: project.name,
                  projectAddress: project.address,
                  stageName: stage?.name || '未知阶段',
                  message: `项目整体还有 ${Math.ceil((endDate - today) / (1000 * 60 * 60 * 24))} 天到期`,
                  projectId: project.id,
                  type: 'endSoon'
                });
              }
            }

            // 检查当前阶段截止日期
            if (stageData.endDate) {
              const stageEndDate = new Date(stageData.endDate);
              if (stageEndDate < today) {
                alerts.push({
                  key: `${project.id}_stageOverdue`,
                  level: 'danger',
                  projectName: project.name,
                  projectAddress: project.address,
                  stageName: stage?.name || '未知阶段',
                  message: `当前阶段"${stage?.name}"已逾期 ${Math.ceil((today - stageEndDate) / (1000 * 60 * 60 * 24))} 天`,
                  projectId: project.id,
                  type: 'stageOverdue'
                });
              } else if (stageEndDate <= threeDaysLater) {
                alerts.push({
                  key: `${project.id}_stageSoon`,
                  level: 'warning',
                  projectName: project.name,
                  projectAddress: project.address,
                  stageName: stage?.name || '未知阶段',
                  message: `当前阶段"${stage?.name}"还有 ${Math.ceil((stageEndDate - today) / (1000 * 60 * 60 * 24))} 天到期`,
                  projectId: project.id,
                  type: 'stageSoon'
                });
              }
            }
          });

          // 紧急预警排前面
          return alerts.sort((a, b) => {
            if (a.level === 'danger' && b.level !== 'danger') return -1;
            if (a.level !== 'danger' && b.level === 'danger') return 1;
            return 0;
          });
        });

        const dangerAlerts = computed(() => urgentAlerts.value.filter(a => a.level === 'danger'));
        const warningAlerts = computed(() => urgentAlerts.value.filter(a => a.level === 'warning'));
        const urgentCount = computed(() => urgentAlerts.value.length);
        const normalCount = computed(() => projects.value.filter(p => p.stageIndex < 7 && !dangerAlerts.value.some(a => a.projectId === p.id)).length);

        const filteredProjects = computed(() => {
          if (!searchKeyword.value) return projects.value;
          return projects.value.filter(p => p.name.includes(searchKeyword.value) || p.address.includes(searchKeyword.value));
        });

        // 流程统计
        const stageStats = computed(() => {
          const stats = stages.map((s, i) => ({ ...s, count: 0, status: 'pending' }));
          projects.value.forEach(p => {
            stats[p.stageIndex] && stats[p.stageIndex].count++;
          });
          return stats;
        });

        // 方法
        const loadData = () => {
          const saved = localStorage.getItem('project_manager_projects');
          if (saved) {
            projects.value = JSON.parse(saved);
          }
          const savedEval = localStorage.getItem('project_manager_evaluations');
          if (savedEval) {
            evaluationRecords.value = JSON.parse(savedEval);
          }
        };

        const saveData = () => {
          localStorage.setItem('project_manager_projects', JSON.stringify(projects.value));
          localStorage.setItem('project_manager_evaluations', JSON.stringify(evaluationRecords.value));
        };

        const getDefaultProject = () => ({
          id: Date.now(),
          name: '',
          address: '',
          manager: '',
          startDate: new Date().toISOString().split('T')[0],
          endDate: '',
          budget: 0,
          remark: '',
          stageIndex: 0,
          stageData: stages.map((s, i) => ({
            startDate: i === 0 ? new Date().toISOString().split('T')[0] : '',
            endDate: '',
            checklist: JSON.parse(JSON.stringify(checklists[i] || []))
          })),
          logs: [{ id: Date.now(), date: new Date().toISOString().split('T')[0], content: '项目创建' }]
        });

        const saveProject = () => {
          if (!projectForm.value.name) {
            ElementPlus.ElMessage.warning('请填写项目名称');
            return;
          }
          if (editingProject.value) {
            Object.assign(editingProject.value, projectForm.value);
          } else {
            const newProject = getDefaultProject();
            Object.assign(newProject, projectForm.value);
            projects.value.push(newProject);
          }
          saveData();
          showAddProject.value = false;
          projectForm.value = { name: '', address: '', manager: '', startDate: '', endDate: '', budget: 0, remark: '' };
          editingProject.value = null;
          ElementPlus.ElMessage.success('保存成功');
        };

        const editProject = (p) => {
          editingProject.value = p;
          projectForm.value = { ...p };
          showAddProject.value = true;
        };

        const deleteProject = (p) => {
          ElementPlus.ElMessageBox.confirm('确定删除该项目吗？', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
          }).then(() => {
            const idx = projects.value.findIndex(x => x.id === p.id);
            if (idx > -1) projects.value.splice(idx, 1);
            saveData();
            ElementPlus.ElMessage.success('已删除');
          }).catch(() => {});
        };

        const viewProject = (p) => {
          currentProject.value = p;
          // 检查并补充子项数据（兼容旧数据）
          if (p && p.stageData) {
            p.stageData.forEach((stage, si) => {
              if (stage.checklist) {
                stage.checklist.forEach(item => {
                  // 如果当前项有对应模板且缺少子项数据，从模板复制
                  const template = checklists[si]?.find(t => t.text === item.text);
                  if (template?.hasSub && !item.hasSub) {
                    item.hasSub = true;
                    item.expanded = false;
                    item.subItems = JSON.parse(JSON.stringify(template.subItems));
                  }
                });
              }
            });
          }
          showProjectDetail.value = true;
        };

        const setProjectStage = (index) => {
          if (currentProject.value) {
            currentProject.value.stageIndex = index;
            if (!currentProject.value.stageData[index].startDate) {
              currentProject.value.stageData[index].startDate = new Date().toISOString().split('T')[0];
            }
            if (index === currentProject.value.stageIndex + 1) {
              currentProject.value.stageData[index - 1].endDate = new Date().toISOString().split('T')[0];
            }
            saveData();
          }
        };

        const getCurrentChecklist = (project) => {
          if (!project || !project.stageData) return [];
          return project.stageData[project.stageIndex]?.checklist || [];
        };

        const getTotalChecklist = (project) => getCurrentChecklist(project).length;
        const getCompletedChecklist = (project) => getCurrentChecklist(project).filter(c => c.done).length;

        const toggleChecklist = (project, stageIdx, itemIdx) => {
          if (project && project.stageData[stageIdx]) {
            const item = project.stageData[stageIdx].checklist[itemIdx];
            item.done = !item.done;
            // 如果有子项，同步更新所有子项状态
            if (item.hasSub && item.subItems) {
              item.subItems.forEach(sub => sub.done = item.done);
            }
            saveData();
          }
        };

        const toggleExpand = (project, stageIdx, itemIdx) => {
          if (project && project.stageData[stageIdx]) {
            const item = project.stageData[stageIdx].checklist[itemIdx];
            item.expanded = !item.expanded;
            saveData();
          }
        };

        const toggleSubItem = (project, stageIdx, itemIdx, subIdx) => {
          if (project && project.stageData[stageIdx]) {
            const item = project.stageData[stageIdx].checklist[itemIdx];
            if (item.subItems && item.subItems[subIdx]) {
              item.subItems[subIdx].done = !item.subItems[subIdx].done;
              // 检查是否所有子项都完成，自动更新父项状态
              item.done = item.subItems.every(sub => sub.done);
              saveData();
            }
          }
        };

        const addLog = () => {
          if (!newLogContent.value.trim()) return;
          if (currentProject.value) {
            currentProject.value.logs.unshift({
              id: Date.now(),
              date: new Date().toISOString().split('T')[0],
              content: newLogContent.value
            });
            saveData();
            newLogContent.value = '';
          }
        };

        const getProgress = (p) => {
          if (!p) return 0;
          return Math.round((p.stageIndex / 7) * 100);
        };

        const getProgressColor = (p) => {
          const prog = getProgress(p);
          if (prog >= 100) return '#52c41a';
          if (prog >= 50) return '#1890ff';
          return '#faad14';
        };

        const getStatusText = (p) => {
          if (p.stageIndex >= 7) return '已完成';
          if (p.endDate && new Date(p.endDate) < new Date()) return '已逾期';
          return stages[p.stageIndex]?.name || '';
        };

        const getStatusClass = (p) => {
          if (p.stageIndex >= 7) return 'completed';
          if (p.endDate && new Date(p.endDate) < new Date()) return 'warning';
          return 'active';
        };

        const getStageName = (p) => stages[p.stageIndex]?.name || '';
        const getStageStatusClass = (p) => {
          if (p.stageIndex >= 7) return 'completed';
          return 'active';
        };

        const viewAlertProject = (alert) => {
          const project = projects.value.find(p => p.id === alert.projectId);
          if (project) {
            currentProject.value = project;
            showProjectDetail.value = true;
          }
        };

        const refreshAlerts = () => {
          // 触发重新计算
          ElementPlus.ElMessage.success('已刷新预警数据');
        };

        const getStageClass = (stage) => {
          if (stage.count === 0) return 'pending';
          if (stage.count > 2) return 'warning';
          return 'active';
        };

        const getProjectStageClass = (project, index) => {
          if (project.stageIndex > index) return 'completed';
          if (project.stageIndex === index) return 'active';
          return 'pending';
        };

        const getStageDate = (project, index) => {
          if (!project || !project.stageData) return '';
          const data = project.stageData[index];
          if (data?.endDate) return '完成:' + data.endDate;
          if (data?.startDate) return '开始:' + data.startDate;
          return '';
        };

        // 选址评估
        const generateEvalScore = () => {
          let score = 0;
          // 基础分
          if (evalForm.value.area >= 200) score += 10;
          if (evalForm.value.area >= 500) score += 10;
          // 租金合理性
          const pricePerSqm = evalForm.value.area > 0 ? evalForm.value.rent / evalForm.value.area : 0;
          if (pricePerSqm <= 50) score += 15;
          else if (pricePerSqm <= 100) score += 10;
          else if (pricePerSqm <= 150) score += 5;
          // 免租期
          score += Math.min(evalForm.value.freeDays / 7, 10);
          // 层高
          if (evalForm.value.height >= 3) score += 5;
          // 电力
          if (evalForm.value.power >= 50) score += 5;
          // 消防
          if (evalForm.value.fireApproval === '已通过') score += 10;
          // 人流
          score += evalForm.value.footTraffic * 5;
          // 交通
          if (evalForm.value.traffic === '优') score += 10;
          else if (evalForm.value.traffic === '良') score += 5;
          // 停车
          if (evalForm.value.parking > 0) score += 5;
          // 竞争
          if (evalForm.value.competitors === 0) score += 10;
          else if (evalForm.value.competitors <= 3) score += 5;

          evalForm.value.score = Math.min(100, Math.round(score));
          ElementPlus.ElMessage.success('评分已更新：' + evalForm.value.score + '分');
        };

        const convertToProject = () => {
          if (!evalForm.value.name || !evalForm.value.address) {
            ElementPlus.ElMessage.warning('请填写场地名称和地址');
            return;
          }
          if (evalForm.value.score === 0) {
            generateEvalScore();
          }

          const newProject = getDefaultProject();
          newProject.name = evalForm.value.name + '装修项目';
          newProject.address = evalForm.value.address;
          newProject.remark = `面积：${evalForm.value.area}㎡ | 租金：¥${evalForm.value.rent}/月 | 评估得分：${evalForm.value.score}分`;

          projects.value.push(newProject);
          saveData();

          // 保存评估记录
          evaluationRecords.value.unshift({
            ...JSON.parse(JSON.stringify(evalForm.value)),
            date: new Date().toISOString().split('T')[0]
          });
          saveData();

          ElementPlus.ElMessage.success('已转为项目！');
          resetEvalForm();
          currentView.value = 'projects';
        };

        const resetEvalForm = () => {
          evalForm.value = {
            name: '',
            address: '',
            area: 0,
            rent: 0,
            deposit: 2,
            freeDays: 0,
            floor: '',
            height: 0,
            load: 0,
            power: 0,
            fireApproval: '已通过',
            propertyType: '国有出让',
            footTraffic: 0,
            competitors: 0,
            traffic: '良',
            parking: 0,
            restrictions: [],
            agentFee: 0,
            transferFee: 0,
            remark: '',
            score: 0
          };
        };

        const loadEvalRecord = (record) => {
          evalForm.value = { ...record };
          currentView.value = 'evaluate';
        };

        // 报告生成
        const generateReport = () => {
          const project = projects.value.find(p => p.id === reportProjectId.value);
          if (!project) {
            reportData.value = null;
            return;
          }

          const stageName = stages[project.stageIndex].name;
          const progress = getProgress(project);

          reportData.value = {
            title: `${project.name} - ${reportType.value === 'weekly' ? '周报' : reportType.value === 'monthly' ? '月报' : '项目总报告'}`,
            generatedAt: new Date().toLocaleString('zh-CN'),
            basic: {
              name: project.name,
              address: project.address,
              stage: stageName,
              progress: progress,
              period: `${project.startDate || '-'} 至 ${project.endDate || '-'}`,
              manager: project.manager || '未指定'
            },
            stages: stages.map((s, i) => {
              const data = project.stageData[i] || {};
              const total = (data.checklist || []).length;
              const done = (data.checklist || []).filter(c => c.done).length;
              return {
                name: s.name,
                status: i < project.stageIndex ? '已完成' : i === project.stageIndex ? '进行中' : '待开始',
                statusClass: i < project.stageIndex ? 'completed' : i === project.stageIndex ? 'active' : 'pending',
                startDate: data.startDate || '-',
                endDate: data.endDate || '-',
                checkRate: total > 0 ? Math.round(done / total * 100) : 0
              };
            }),
            nextPlan: project.stageIndex < 7 ? `下一步：完成"${stages[project.stageIndex + 1]?.name}"阶段工作` : '项目已全部完成',
            risks: [
              progress < 30 && project.startDate ? '项目启动阶段，需加快进度' : null,
              project.endDate && new Date(project.endDate) < new Date() ? '项目已逾期，需要及时跟进' : null,
              project.stageData[project.stageIndex]?.checklist?.filter(c => !c.done).length > 5 ? '当前阶段未完成项较多，需重点关注' : null
            ].filter(Boolean),
            budget: {
              total: project.budget || 0,
              used: Math.round((project.budget || 0) * progress / 100)
            }
          };
        };

        const exportReport = () => {
          if (!reportData.value) return;
          const content = JSON.stringify(reportData.value, null, 2);
          const blob = new Blob([content], { type: 'application/json' });
          const url = URL.createObjectURL(blob);
          const a = document.createElement('a');
          a.href = url;
          a.download = `${reportData.value.basic.name}_报告_${new Date().toISOString().split('T')[0]}.json`;
          a.click();
          URL.revokeObjectURL(url);
          ElementPlus.ElMessage.success('报告已导出');
        };

        onMounted(() => {
          loadData();
          // 如果没有项目，添加示例
          if (projects.value.length === 0) {
            const demoProject = getDefaultProject();
            demoProject.name = '广州天河城旗舰店装修';
            demoProject.address = '广州市天河区天河路208号天河城广场';
            demoProject.manager = '张经理';
            demoProject.startDate = '2026-03-01';
            demoProject.endDate = '2026-04-26'; // 今日到期，触发预警
            demoProject.budget = 500000;
            demoProject.stageIndex = 3;
            demoProject.stageData[3].startDate = '2026-04-01';
            demoProject.stageData[3].endDate = '2026-04-20'; // 已逾期
            projects.value.push(demoProject);
            saveData();
          }
          // 打开页面时检查预警，有紧急预警则弹出
          setTimeout(() => {
            if (dangerAlerts.value.length > 0) {
              showAlertModal.value = true;
            }
          }, 500);
        });

        return {
          stages, projects, evaluationRecords, currentView, searchKeyword,
          showAddProject, showProjectDetail, editingProject, currentProject,
          newLogContent, projectForm, evalForm, reportProjectId, reportType, reportData,
          activeProjects, completedProjects, overdueProjects, recentProjects,
          filteredProjects, stageStats,
          urgentAlerts, dangerAlerts, warningAlerts, urgentCount, normalCount,
          showAlertModal,
          saveProject, editProject, deleteProject, viewProject, setProjectStage,
          getCurrentChecklist, getTotalChecklist, getCompletedChecklist, toggleChecklist,
          toggleExpand, toggleSubItem,
          addLog, getProgress, getProgressColor, getStatusText, getStatusClass,
          getStageName, getStageStatusClass, getStageClass, getProjectStageClass, getStageDate,
          viewAlertProject, refreshAlerts,
          generateEvalScore, convertToProject, resetEvalForm, loadEvalRecord,
          generateReport, exportReport
        };
      }
    });

    app.use(ElementPlus);
    app.mount('#app');
  </script>
</body>
</html>
