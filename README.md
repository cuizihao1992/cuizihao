    <!DOCTYPE html>
    <html lang="zh-CN">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>转正答辩PPT - HTML重设计</title>
        <style>
            * {
                margin: 0;
                padding: 0;
                box-sizing: border-box;
            }

            body {
                font-family: "Microsoft YaHei", "微软雅黑", Arial, sans-serif;
                background: #ffffff;
                overflow-x: hidden;
            }

            .presentation-container {
                display: flex;
                width: 100vw;
                height: 100vh;
                overflow: hidden;
            }

            /* 左侧导航 */
            .sidebar {
                width: 250px;
                background: rgba(255, 255, 255, 0.95);
                border-right: 2px solid #e5e7eb;
                overflow-y: auto;
                padding: 20px;
                box-shadow: 2px 0 10px rgba(0, 0, 0, 0.1);
                transition: all 0.3s ease;
            }

            /* 折叠状态：目录完全收起，不占宽度 */
            .sidebar.collapsed {
                width: 0;
                padding: 0;
                border-right: none;
                box-shadow: none;
                overflow: hidden;
            }

            .sidebar.collapsed * {
                display: none;
            }

            .nav-item {
                padding: 12px 16px;
                margin-bottom: 8px;
                border-radius: 8px;
                cursor: pointer;
                transition: all 0.3s ease;
                border-left: 4px solid transparent;
                background: #f8fafc;
            }

            .nav-item:hover {
                background: #e0e7ff;
                transform: translateX(4px);
            }

            .nav-item.active {
                background: linear-gradient(90deg, #1E3A8A, #0285CA);
                color: white;
                border-left-color: #0285CA;
                font-weight: bold;
            }

            .nav-item-number {
                display: inline-block;
                width: 24px;
                height: 24px;
                background: rgba(255, 255, 255, 0.3);
                border-radius: 50%;
                text-align: center;
                line-height: 24px;
                margin-right: 8px;
                font-size: 12pt;
            }

            .nav-item.active .nav-item-number {
                background: rgba(255, 255, 255, 0.5);
            }

            /* 主内容区 */
            .main-content {
                flex: 1;
                display: flex;
                justify-content: center;
                overflow-y: auto;
                position: relative;
            }

            .slide-wrapper {
                width: 100%;
                display: flex;
                flex-direction: column;
                align-items: center;
                gap: 24px;
                padding: 24px 0;
            }

            /* 单页以 PPT 横版为参考宽度，但高度自适应，避免内容被裁切 */
            .slide {
                width: 1600px;
                max-width: 96vw; /* 目录展开/收起时都能尽量占满可视区域 */
                padding: 40px 60px;
                background: linear-gradient(135deg, #ffffff 0%, #f8fafc 100%);
                box-shadow: 0 0 30px rgba(0, 0, 0, 0.1);
                margin: 0;
            }

            /* 通用样式 */
            .slide-title {
                font-size: 36pt;
                font-weight: bold;
                color: #1E3A8A;
                margin-bottom: 28px;
                padding-bottom: 16px;
                border-bottom: 4px solid;
                border-image: linear-gradient(90deg, #1E3A8A, #0285CA) 1;
                display: flex;
                align-items: center;
                gap: 16px;
            }

            .slide-title::before {
                content: '';
                width: 6px;
                height: 40px;
                background: linear-gradient(180deg, #1E3A8A, #0285CA);
                border-radius: 3px;
            }

            .section {
                margin-bottom: 22px;
            }

            .section-title {
                font-size: 24pt;
                font-weight: bold;
                color: #1E3A8A;
                margin-bottom: 16px;
                display: flex;
                align-items: center;
                gap: 12px;
            }

            .section-title::before {
                content: '';
                width: 4px;
                height: 24px;
                background: #0285CA;
                border-radius: 2px;
            }

            .section-title.problem {
                color: #DC2626;
            }

            .section-title.problem::before {
                background: #DC2626;
            }

            .section-title.solution {
                color: #0285CA;
            }

            .content-grid {
                display: grid;
                grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
                gap: 20px;
                margin-top: 20px;
            }

            .card {
                background: white;
                border-radius: 12px;
                padding: 20px;
                box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
                border-left: 4px solid #0285CA;
                transition: all 0.3s ease;
            }

            .card:hover {
                transform: translateY(-4px);
                box-shadow: 0 8px 20px rgba(0, 0, 0, 0.15);
            }

            .card-title {
                font-size: 18pt;
                font-weight: bold;
                color: #1E3A8A;
                margin-bottom: 12px;
            }

            .card-content {
                font-size: 14pt;
                color: #374151;
                line-height: 1.8;
            }

            .card-content ul {
                margin-left: 20px;
                margin-top: 8px;
            }

            .card-content li {
                margin-bottom: 8px;
            }

            .highlight {
                color: #0285CA;
                font-weight: bold;
            }

            .stat-box {
                background: linear-gradient(135deg, #1E3A8A, #0285CA);
                color: white;
                padding: 24px;
                border-radius: 12px;
                text-align: center;
                margin: 20px 0;
            }

            .stat-number {
                font-size: 36pt;
                font-weight: bold;
                margin-bottom: 8px;
            }

            .stat-label {
                font-size: 18pt;
            }

            .workflow {
                display: flex;
                flex-direction: column;
                gap: 16px;
                margin: 20px 0;
            }

            .workflow-item {
                display: flex;
                align-items: center;
                gap: 16px;
                padding: 16px;
                background: rgba(30, 58, 138, 0.05);
                border-radius: 8px;
                border-left: 4px solid #1E3A8A;
            }

            .workflow-number {
                width: 40px;
                height: 40px;
                background: linear-gradient(135deg, #1E3A8A, #0285CA);
                color: white;
                border-radius: 50%;
                display: flex;
                align-items: center;
                justify-content: center;
                font-size: 18pt;
                font-weight: bold;
                flex-shrink: 0;
            }

            .workflow-content {
                flex: 1;
                font-size: 16pt;
                color: #374151;
            }

            .arrow {
                text-align: center;
                color: #0285CA;
                font-size: 24pt;
                margin: 8px 0;
            }

            /* 流程行样式（项目支撑步骤专用） */
            .workflow-line {
                display: flex;
                align-items: center;
                gap: 16px;
                padding: 16px 24px;
                border-radius: 16px;
                background: #F5F7FB;
                box-shadow: 0 2px 6px rgba(15, 23, 42, 0.05);
            }

            .workflow-line .workflow-number {
                width: 44px;
                height: 44px;
                border-radius: 50%;
                background: linear-gradient(135deg, #1E3A8A, #0285CA);
                color: #ffffff;
                display: flex;
                align-items: center;
                justify-content: center;
                font-size: 18pt;
                font-weight: bold;
                flex-shrink: 0;
            }

            .workflow-line .workflow-content {
                font-size: 15pt;
                color: #111827;
            }

            .workflow-arrow-down {
                text-align: center;
                font-size: 24pt;
                color: #0285CA;
                margin: 10px 0;
            }

            /* 项目支撑页 左文右图布局 */
            .project-layout {
                display: grid;
                grid-template-columns: 1.4fr 1fr;
                gap: 24px;
                margin-top: 20px;
                align-items: stretch;
            }

            .project-left {
                display: flex;
                flex-direction: column;
                gap: 12px;
            }

            .project-right {
                display: flex;
                align-items: center;
                justify-content: center;
            }

            .image-placeholder {
                width: 100%;
                height: 260px;
                border-radius: 16px;
                border: 1px dashed #CBD5E1;
                background: #F9FAFB;
                display: flex;
                align-items: center;
                justify-content: center;
                color: #9CA3AF;
                font-size: 14pt;
                text-align: center;
                padding: 16px;
            }

            .two-column {
                display: grid;
                grid-template-columns: 1fr 1fr;
                gap: 30px;
                margin: 20px 0;
            }

            .three-column {
                display: grid;
                grid-template-columns: repeat(3, 1fr);
                gap: 20px;
                margin: 20px 0;
            }

            /* 架构对比表 */
            .compare-table {
                width: 100%;
                border-collapse: collapse;
                margin-top: 6px;
                font-size: 11pt;
                color: #374151;
            }

            .compare-table th,
            .compare-table td {
                border: 1px solid #e5e7eb;
                padding: 6px 8px;
                vertical-align: top;
            }

            .compare-table th {
                background: #f3f4f6;
                font-weight: bold;
                text-align: left;
            }

            .compare-table td strong {
                color: #111827;
            }

            /* 右下角控制按钮、页码等已不再使用（单页滚动展示） */

            /* 特殊样式 */
            .badge {
                display: inline-block;
                padding: 4px 12px;
                background: linear-gradient(135deg, #0285CA, #1E3A8A);
                color: white;
                border-radius: 12px;
                font-size: 12pt;
                font-weight: bold;
                margin: 0 4px;
            }

            .code-block {
                background: #1e1e1e;
                color: #d4d4d4;
                padding: 20px;
                border-radius: 8px;
                font-family: 'Consolas', 'Monaco', monospace;
                font-size: 12pt;
                line-height: 1.6;
                margin: 16px 0;
                overflow-x: auto;
            }

            .progress-bar {
                height: 8px;
                background: #e5e7eb;
                border-radius: 4px;
                overflow: hidden;
                margin: 8px 0;
            }

            .progress-fill {
                height: 100%;
                background: linear-gradient(90deg, #1E3A8A, #0285CA);
                transition: width 0.3s ease;
            }

            /* ECharts 容器 */
            .chart-container {
                width: 100%;
                height: 320px;
                margin-top: 24px;
                background: #ffffff;
                border-radius: 12px;
                box-shadow: 0 4px 12px rgba(0, 0, 0, 0.08);
                padding: 12px;
            }

            /* 目录折叠/展开按钮 */
            .toc-toggle {
                position: fixed;
                left: 10px;
                top: 10px;
                z-index: 1000;
                background: rgba(255, 255, 255, 0.95);
                border-radius: 999px;
                padding: 4px 12px;
                font-size: 11pt;
                color: #1E3A8A;
                cursor: pointer;
                box-shadow: 0 2px 6px rgba(0, 0, 0, 0.2);
                user-select: none;
            }
        </style>
    </head>
    <body>
        <div class="toc-toggle" onclick="toggleSidebar()">收起目录</div>
        <div class="presentation-container">
            <!-- 左侧导航 -->
            <div class="sidebar">
                <!-- ① 工作成果 -->
                <div class="nav-item active" data-target="sec-work-overview" onclick="scrollToSection('sec-work-overview')">
                    <span class="nav-item-number">1</span>
                    ①-0 工作成果-总览
                </div>
                <div class="nav-item" data-target="sec-work-tech" onclick="scrollToSection('sec-work-tech')">
                    <span class="nav-item-number">2</span>
                    ①-1 工作成果-技术选型与架构
                </div>
                <div class="nav-item" data-target="sec-work-arch" onclick="scrollToSection('sec-work-arch')">
                    <span class="nav-item-number">3</span>
                    ①-2 工作成果-架构设计-3D GIS
                </div>
                <div class="nav-item" data-target="sec-work-project-fujian" onclick="scrollToSection('sec-work-project-fujian')">
                    <span class="nav-item-number">4</span>
                    ①-3 工作成果-项目支撑-福建
                </div>
                <div class="nav-item" data-target="sec-work-project-as" onclick="scrollToSection('sec-work-project-as')">
                    <span class="nav-item-number">5</span>
                    ①-4 工作成果-项目支撑-AS通感
                </div>
                <div class="nav-item" data-target="sec-work-ai" onclick="scrollToSection('sec-work-ai')">
                    <span class="nav-item-number">6</span>
                    ①-5 工作成果-工程实践-AI辅助开发
                </div>
                <div class="nav-item" data-target="sec-work-dod" onclick="scrollToSection('sec-work-dod')">
                    <span class="nav-item-number">7</span>
                    ①-6 工作成果-工程实践-DoD流程实践
                </div>

                <!-- ② 问题思考 -->
                <div class="nav-item" data-target="sec-think-arch" onclick="scrollToSection('sec-think-arch')">
                    <span class="nav-item-number">8</span>
                    ②-1 问题思考-架构与技术栈
                </div>
                <div class="nav-item" data-target="sec-think-test" onclick="scrollToSection('sec-think-test')">
                    <span class="nav-item-number">9</span>
                    ②-2 问题思考-测试与AI
                </div>

                <!-- ③ 未来规划 -->
                <div class="nav-item" data-target="sec-plan-model" onclick="scrollToSection('sec-plan-model')">
                    <span class="nav-item-number">10</span>
                    ③-1 未来规划-模型处理能力
                </div>
                <div class="nav-item" data-target="sec-plan-ue" onclick="scrollToSection('sec-plan-ue')">
                    <span class="nav-item-number">11</span>
                    ③-2 未来规划-UE数字孪生
                </div>
                <div class="nav-item" data-target="sec-plan-future" onclick="scrollToSection('sec-plan-future')">
                    <span class="nav-item-number">12</span>
                    ③-3 未来规划-发展路线
                </div>
                <div class="nav-item" data-target="sec-summary" onclick="scrollToSection('sec-summary')">
                    <span class="nav-item-number">13</span>
                    ③-4 未来规划-总结与思考
                </div>
            </div>

            <!-- 主内容区 -->
            <div class="main-content">
                <div class="slide-wrapper" id="slide-wrapper">
                    <!-- ①-0 工作成果-总览 -->
                    <div class="slide" id="sec-work-overview">
                        <div class="slide-title">工作成果-总览</div>
                        <div class="content-grid">
                            <div class="card">
                                <div class="card-title">🧱 架构与技术选型</div>
                                <div class="card-content">
                                    <ul>
                                        <li>完成 Cesium 技术选型，多维度对比 StreetsGL 等方案</li>
                                        <li>在三维侧设计并落地一套 3D GIS 前端架构（Viewer / Manager / Module / Service 分层），用统一模式管理所有三维场景</li>
                                        <li>为后续通信可视化、6G 空天地一体化等场景提供可复用的三维技术底座</li>
                                    </ul>
                                </div>
                            </div>
                            <div class="card">
                                <div class="card-title">📊 项目支撑与落地</div>
                                <div class="card-content">
                                    <ul>
                                        <li>在福建移动数字孪生平台中落地统一图层与数据抽象</li>
                                        <li>在 AS 通感/室内通感项目中验证模型加载、坐标偏移等能力</li>
                                        <li>用真实业务场景验证架构设计与技术选型的可行性</li>
                                    </ul>
                                </div>
                            </div>
                            <div class="card">
                                <div class="card-title">🛠️ 工程实践与质量</div>
                                <div class="card-content">
                                    <ul>
                                        <li>使用 AI 辅助开发与测试，建立较完善的单元测试体系</li>
                                        <li>理解并实践公司 DoD 流程，从需求实例化到验收全流程落地</li>
                                        <li>在交付压力下兼顾开发效率与代码质量</li>
                                    </ul>
                                </div>
                            </div>
                            <div class="card">
                                <div class="card-title">🚀 未来规划与探索</div>
                                <div class="card-content">
                                    <ul>
                                        <li>明确短中期重点：模型处理能力建设、统一 2D/3D 业务封装</li>
                                        <li>开展 UE 数字孪生方案的前期技术预研，作为长期探索方向</li>
                                        <li>围绕 6G 通信场景，规划个人在 WebGIS 3D 与平台架构方向的成长路径</li>
                                    </ul>
                                </div>
                            </div>
                        </div>
                        <div class="stat-box">
                            <div class="stat-label">试用期内，初步跑通了从架构到项目落地和工程实践的一条完整链路</div>
                        </div>
                    </div>

                    <!-- ①-1 工作成果-技术选型与架构 -->
                    <div class="slide" id="sec-work-tech">
                        <div class="slide-title">技术选型与架构设计</div>
                        <div class="three-column">
                            <div class="card">
                                <div class="card-title">🔍 技术选型预研</div>
                                <div class="card-content">
                                    <ul>
                                        <li>1周深度调研</li>
                                        <li>Streets GL vs Cesium</li>
                                        <li>多维度对比分析</li>
                                        <li>决策：<span class="highlight">Cesium</span></li>
                                    </ul>
                                </div>
                            </div>
                            <div class="card">
                                <div class="card-title">🏗️ SDK架构设计</div>
                                <div class="card-content">
                                    <ul>
                                        <li>模块化、高内聚低耦合</li>
                                        <li>统一API接口</li>
                                        <li>预计提升效率<span class="highlight">30%+</span></li>
                                        <li>建立可复用技术资产</li>
                                    </ul>
                                </div>
                            </div>
                            <div class="card">
                                <div class="card-title">💡 创新方案</div>
                                <div class="card-content">
                                    <ul>
                                        <li>离线地图方案设计</li>
                                        <li>支持批量下载、断点续传</li>
                                    </ul>
                                </div>
                            </div>
                        </div>
                        <div class="section">
                            <div class="section-title">决策依据</div>
                            <div class="content-grid">
                                <div class="card">
                                    <div class="card-content">
                                        <p><strong>公司6G业务需求</strong></p>
                                        <p>空天地一体化场景</p>
                                    </div>
                                </div>
                                <div class="card">
                                    <div class="card-content">
                                        <p><strong>Cesium技术背景</strong></p>
                                        <p>AGI公司，航空航天和国防领域</p>
                                        <p>天然支持卫星数据可视化</p>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="section">
                            <div class="section-title">技术选型对比</div>
                            <table class="compare-table">
                                <thead>
                                    <tr>
                                        <th>对比维度</th>
                                        <th>Cesium</th>
                                        <th>StreetsGL</th>
                                        <th>其他三维GIS框架（ArcGIS 3D / SuperMap 等）</th>
                                    </tr>
                                </thead>
                                <tbody>
                                    <tr>
                                        <td><strong>技术成熟度 & 生态</strong></td>
                                        <td>开源多年，三维地球/卫星项目多；3D Tiles、glTF 等标准支持好。</td>
                                        <td>生态相对新，社区小；标准格式支持不完整。</td>
                                        <td>商业平台成熟度高，但闭源，生态相对封闭。</td>
                                    </tr>
                                    <tr>
                                        <td><strong>通信/6G 场景匹配度</strong></td>
                                        <td>原生三维地球 + 轨道/卫星，贴合空天地一体化、数字孪生网络等场景。</td>
                                        <td>更偏街景/地面视角，缺少大范围通信网络、卫星案例。</td>
                                        <td>偏通用政企/城市管理，对通信/6G 需额外定制与集成。</td>
                                    </tr>
                                    <tr>
                                        <td><strong>开发效率 & 上手</strong></td>
                                        <td>能力开箱即用，示例多，团队已有实践，上手成本可控。</td>
                                        <td>文档/示例少，新问题多靠自研，试错成本高。</td>
                                        <td>自带平台工具，上手快，但二次开发强依赖厂商生态与特定 IDE。</td>
                                    </tr>
                                    <tr>
                                        <td><strong>与现有架构的关系</strong></td>
                                        <td>可直接作为 z3d2 底层，引擎与 Viewer/Manager/Module 分层自然匹配。</td>
                                        <td>如改用 StreetsGL，需要大幅调整现有架构与工具链，迁移成本高。</td>
                                        <td>更像外部平台而非轻量 SDK，引入后会提高整体架构复杂度。</td>
                                    </tr>
                                </tbody>
                            </table>
                            <p style="margin-top: 12px; font-size: 13pt; color: #4B5563;">
                                <strong>结论：</strong>在当前阶段，只有 <span class="highlight">Cesium</span> 同时满足
                                <span class="highlight">6G 场景匹配度</span>、
                                <span class="highlight">GIS 能力成熟度</span>、
                                <span class="highlight">开发效率</span> 和
                                <span class="highlight">生态/格式标准</span> 等关键要求，
                                且能与现有 z3d2 架构自然融合，因此选择以 Cesium 作为 3D GIS 底层技术栈。
                            </p>
                        </div>
                    </div>

                    <!-- ①-2 工作成果-架构设计-3D GIS -->
                    <div class="slide" id="sec-work-arch">
                        <div class="slide-title">架构设计-3D GIS前端架构</div>
                        <div class="section">
                            <div class="section-title">设计原则</div>
                            <div class="content-grid">
                                <div class="card">
                                    <div class="card-content">
                                        <p>可扩展的模块化3D地理引擎</p>
                                    </div>
                                </div>
                                <div class="card">
                                    <div class="card-content">
                                        <p>单一Viewer入口</p>
                                    </div>
                                </div>
                                <div class="card">
                                    <div class="card-content">
                                        <p>职责清晰分层</p>
                                    </div>
                                </div>
                                <div class="card">
                                    <div class="card-content">
                                        <p>插件式图层与模块</p>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="section">
                            <div class="section-title">核心架构</div>
                            <div class="workflow">
                                <div class="workflow-item">
                                    <div class="workflow-number">V</div>
                                    <div class="workflow-content"><strong>Viewer</strong>：统一调度，生命周期与组合根</div>
                                </div>
                                <div class="arrow">↓</div>
                                <div class="workflow-item">
                                    <div class="workflow-number">M</div>
                                    <div class="workflow-content"><strong>Manager</strong>：数据加载、状态管理</div>
                                </div>
                                <div class="arrow">↓</div>
                                <div class="workflow-item">
                                    <div class="workflow-number">M</div>
                                    <div class="workflow-content"><strong>Module</strong>：真正的WebGL渲染</div>
                                </div>
                                <div class="arrow">↓</div>
                                <div class="workflow-item">
                                    <div class="workflow-number">S</div>
                                    <div class="workflow-content"><strong>Service</strong>：校验、事件、缩放级别控制</div>
                                </div>
                            </div>
                        </div>
                        <div class="section">
                            <div class="section-title">新旧三维架构对比</div>
                            <table class="compare-table">
                                <thead>
                                    <tr>
                                        <th>维度</th>
                                        <th>老架构（<code>src/…</code>）</th>
                                        <th>新架构（<code>src/z3d2/…</code>）</th>
                                    </tr>
                                </thead>
                                <tbody>
                                    <tr>
                                        <td><strong>组织形式</strong></td>
                                        <td>按功能平铺目录（viewer / entity / tool / utils…），模块之间缺少统一入口与依赖管理。</td>
                                        <td>按层次划分（core / managers / modules / utils），有清晰的分层和依赖方向。</td>
                                    </tr>
                                    <tr>
                                        <td><strong>入口模式</strong></td>
                                        <td>通过 <code>initViewer</code> 等函数直接操作 Cesium，把DOM检查、底图选择、Viewer初始化等堆在一起。</td>
                                        <td><code>Z3D2.Viewer</code> 类作为唯一入口，集中完成 Cesium 初始化、Manager 装配、事件系统等。</td>
                                    </tr>
                                    <tr>
                                        <td><strong>图层管理</strong></td>
                                        <td>各处直接访问 <code>viewer.entities</code> / <code>imageryLayers</code>，每个模块自己管理生命周期。</td>
                                        <td><code>LayerManager</code> 统一管理 Imagery/Tiles/Heatmap/Vector/Cell/Grid 等图层的创建与销毁。</td>
                                    </tr>
                                    <tr>
                                        <td><strong>事件系统</strong></td>
                                        <td>在各个文件中分散调用 <code>setInputAction</code>，没有统一的事件优先级和冒泡控制。</td>
                                        <td><code>EventManager</code> 统一管理点击、移动等事件，集中处理优先级和分发。</td>
                                    </tr>
                                    <tr>
                                        <td><strong>复用与工具</strong></td>
                                        <td>存在多套类似工具脚本（多个 <code>*_mathTools.js</code>），演进主要靠复制粘贴。</td>
                                        <td>Utils/Modules 统一收口，公共能力沉淀为可复用组件，通过依赖注入按需组合。</td>
                                    </tr>
                                    <tr>
                                        <td><strong>测试与维护</strong></td>
                                        <td>函数式脚本多、耦合度高，系统化测试覆盖较难。</td>
                                        <td>Manager/Utils 为独立模块，在 <code>tests/z3d2/**</code> 下配套单元测试，更利于重构与回归。</td>
                                    </tr>
                                </tbody>
                            </table>
                            <p style="margin-top: 12px; font-size: 13pt; color: #4B5563;">
                                当前架构升级<strong>优先覆盖三维</strong>部分，在保证交付的前提下，把 3D 从“脚本堆叠”演进为“可维护、可复用的 SDK”。后续会在此基础上抽象业务层，逐步统一 2D / 3D 的业务接口。
                            </p>
                        </div>
                    </div>

                    <!-- ①-3 工作成果-项目支撑-福建 -->
                    <div class="slide" id="sec-work-project-fujian">
                        <div class="slide-title">工作成果-项目支撑-福建移动数字孪生平台</div>
                        <div class="section">
                            <div class="card">
                                <div class="card-title">🏢 福建移动数字孪生平台</div>
                                <div class="card-content">
                                    <p><strong>核心功能：</strong></p>
                                    <ul>
                                        <li>建筑物渐变着色</li>
                                        <li>热力图加载与优化</li>
                                        <li>小区图层管理</li>
                                        <li>建筑高亮展示</li>
                                        <li>栅格图层系统</li>
                                    </ul>
                                    <p style="margin-top: 16px;"><strong>技术亮点：</strong></p>
                                    <ul>
                                        <li>模仿二维抽象出图层，三维图层体系与二维保持一致</li>
                                        <li>多数据源支持，便于后续叠加通信数据</li>
                                        <li>支持图层动态更新，适配运营侧频繁调整</li>
                                    </ul>
                                </div>
                            </div>
                        </div>
                        <div class="project-layout">
                            <div class="project-left">
                                <div class="workflow">
                                    <div class="workflow-line">
                                        <div class="workflow-number">1</div>
                                        <div class="workflow-content">需求分析 → 理解运营、规划侧对三维展示与图层体系的诉求</div>
                                    </div>
                                    <div class="workflow-arrow-down">↓</div>
                                    <div class="workflow-line">
                                        <div class="workflow-number">2</div>
                                        <div class="workflow-content">功能开发 → 基于统一架构实现建筑、热力、小区、栅格等图层</div>
                                    </div>
                                    <div class="workflow-arrow-down">↓</div>
                                    <div class="workflow-line">
                                        <div class="workflow-number">3</div>
                                        <div class="workflow-content">问题定位 → 结合日志与场景快速定位渲染/数据问题</div>
                                    </div>
                                    <div class="workflow-arrow-down">↓</div>
                                    <div class="workflow-line">
                                        <div class="workflow-number">4</div>
                                        <div class="workflow-content">系统优化 → 通过抽象图层与数据源，提升复用与可维护性</div>
                                    </div>
                                </div>
                            </div>
                            <div class="project-right">
                                <div class="image-placeholder">
                                    福建移动数字孪生平台三维效果截图占位<br/>
                                    （正式PPT中可替换为实际效果图）
                                </div>
                            </div>
                        </div>
                    </div>

                    <!-- ①-4 工作成果-项目支撑-AS通感 -->
                    <div class="slide" id="sec-work-project-as">
                        <div class="slide-title">工作成果-项目支撑-AS通感/室内通感项目</div>
                        <div class="section">
                            <div class="card">
                                <div class="card-title">📡 AS通感/室内通感项目</div>
                                <div class="card-content">
                                    <p><strong>核心功能：</strong></p>
                                    <ul>
                                        <li>3D模型加载（多格式支持）</li>
                                        <li>坐标自动偏移</li>
                                        <li>新功能通过可选参数实现</li>
                                        <li>Token失效问题修复</li>
                                    </ul>
                                    <p style="margin-top: 16px;"><strong>技术亮点：</strong></p>
                                    <ul>
                                        <li>优化模型加载流程，缩短场景加载时间</li>
                                        <li>模型位置调整 + 自动偏移，结合GPS/坐标系知识保证精度</li>
                                        <li>通过可选参数实现新功能，兼顾向后兼容</li>
                                    </ul>
                                </div>
                            </div>
                        </div>
                        <div class="workflow">
                            <div class="workflow-item">
                                <div class="workflow-number">1</div>
                                <div class="workflow-content">需求分析 → 通感/室内场景对模型精度与加载性能的要求</div>
                            </div>
                            <div class="arrow">↓</div>
                            <div class="workflow-item">
                                <div class="workflow-number">2</div>
                                <div class="workflow-content">功能开发 → 在统一架构下扩展模型加载、坐标偏移等能力</div>
                            </div>
                            <div class="arrow">↓</div>
                            <div class="workflow-item">
                                <div class="workflow-number">3</div>
                                <div class="workflow-content">问题定位 → Token 失效导致页面刷新问题的快速排查与修复</div>
                            </div>
                            <div class="arrow">↓</div>
                            <div class="workflow-item">
                                <div class="workflow-number">4</div>
                                <div class="workflow-content">系统优化 → 通过参数化设计保证老版本兼容与新功能扩展</div>
                            </div>
                        </div>
                    </div>

                    <!-- ①-5 工作成果-工程实践-AI辅助开发 -->
                    <div class="slide" id="sec-work-ai">
                        <div class="slide-title">AI辅助开发成果</div>
                        <div class="stat-box">
                            <div class="stat-number">5.3万行</div>
                            <div class="stat-label">高质量代码，测试覆盖 1:1.7</div>
                        </div>
                        <div class="two-column">
                            <div class="card">
                                <div class="card-title">📝 业务代码</div>
                                <div class="card-content">
                                    <p><strong>总计：19,490行</strong></p>
                                    <div class="progress-bar">
                                        <div class="progress-fill" style="width: 46%;"></div>
                                    </div>
                                    <ul>
                                        <li>其他工具类和模块：9,086行 (46%)</li>
                                        <li>3D瓦片管理器：2,604行 (13%)</li>
                                        <li>视图核心类：1,928行 (10%)</li>
                                        <li>栅格管理器：1,921行 (10%)</li>
                                        <li>矢量管理器：1,354行 (7%)</li>
                                        <li>图层管理器：1,046行 (5%)</li>
                                    </ul>
                                </div>
                            </div>
                            <div class="card">
                                <div class="card-title">✅ 测试代码</div>
                                <div class="card-content">
                                    <p><strong>总计：33,241行</strong></p>
                                    <p><strong>测试用例：2,875个</strong></p>
                                    <div class="progress-bar">
                                        <div class="progress-fill" style="width: 76%;"></div>
                                    </div>
                                    <ul>
                                        <li>其他测试文件：25,381行 (76%)</li>
                                        <li>3D瓦片管理器测试：1,763行 (5%)</li>
                                        <li>小区管理器测试：1,653行 (5%)</li>
                                        <li>栅格管理器测试：1,258行 (4%)</li>
                                    </ul>
                                </div>
                            </div>
                        </div>
                        <div class="chart-container">
                            <div id="codeChart" style="width: 100%; height: 100%;"></div>
                        </div>
                    </div>

                    <!-- ③-1 未来规划-模型处理能力 -->
                    <div class="slide" id="sec-plan-model">
                        <div class="slide-title">模型处理能力建设</div>
                        <div class="section">
                            <div class="section-title">格式转换能力 <span class="badge">100%自动化</span></div>
                            <div class="content-grid">
                                <div class="card">
                                    <div class="card-title">OSGB → 3D Tiles</div>
                                    <div class="card-content">
                                        <ul>
                                            <li>批次表纹理处理</li>
                                            <li>坐标系校正</li>
                                            <li>属性保留</li>
                                        </ul>
                                    </div>
                                </div>
                                <div class="card">
                                    <div class="card-title">OBJ/FBX/DAE → B3DM</div>
                                    <div class="card-content">
                                        <ul>
                                            <li>gITF + 批次表</li>
                                        </ul>
                                    </div>
                                </div>
                                <div class="card">
                                    <div class="card-title">DEM → Cesium terrain</div>
                                    <div class="card-content">
                                        <ul>
                                            <li>GeoTIFF、ASC、IMG</li>
                                            <li>quantized-mesh-1.0</li>
                                        </ul>
                                    </div>
                                </div>
                                <div class="card">
                                    <div class="card-title">BIM → 3D Tiles</div>
                                    <div class="card-content">
                                        <ul>
                                            <li>Revit、IFC、Tekla</li>
                                            <li>构件属性JSON</li>
                                        </ul>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="two-column">
                            <div class="card">
                                <div class="card-title">📦 模型压缩</div>
                                <div class="card-content">
                                    <ul>
                                        <li>减少体积，减少资源和传输成本</li>
                                        <li>提升渲染效率，降低硬件要求</li>
                                        <li>自动压缩，无需人工介入</li>
                                    </ul>
                                </div>
                            </div>
                            <div class="card">
                                <div class="card-title">🆕 新格式支持</div>
                                <div class="card-content">
                                    <ul>
                                        <li>BIM（建筑信息模型）</li>
                                    </ul>
                                </div>
                            </div>
                        </div>
                    </div>

                    <!-- 第7页：UE数字孪生方案探索 -->
                    <div class="slide" id="sec-plan-ue">
                        <div class="slide-title">UE数字孪生方案探索</div>
                        <div class="content-grid">
                            <div class="card">
                                <div class="card-title">🎮 技术选型</div>
                                <div class="card-content">
                                    <ul>
                                        <li>Unreal Engine 5</li>
                                        <li>Lumen全局光照</li>
                                        <li>Nanite虚拟几何</li>
                                        <li>World Partition大世界</li>
                                    </ul>
                                </div>
                            </div>
                            <div class="card">
                                <div class="card-title">💪 核心能力</div>
                                <div class="card-content">
                                    <ul>
                                        <li>大规模场景支持</li>
                                        <li>高画质渲染</li>
                                        <li>物理仿真引擎</li>
                                        <li>实时数据同步</li>
                                    </ul>
                                </div>
                            </div>
                            <div class="card">
                                <div class="card-title">📡 通信应用场景</div>
                                <div class="card-content">
                                    <ul>
                                        <li>基站规划可视化</li>
                                        <li>网络运维监控</li>
                                        <li>信号覆盖仿真</li>
                                        <li>故障定位分析</li>
                                    </ul>
                                </div>
                            </div>
                            <div class="card">
                                <div class="card-title">⚙️ 实施考虑</div>
                                <div class="card-content">
                                    <ul>
                                        <li>技术探索方向，为未来储备</li>
                                        <li>需评估市场需求、人力投入、硬件资源</li>
                                        <li>短期聚焦WebGIS，UE作为长期技术方向</li>
                                    </ul>
                                </div>
                            </div>
                        </div>
                    </div>

                    <!-- 第8页：DoD流程学习与实践 -->
                    <div class="slide" id="sec-work-dod">
                        <div class="slide-title">DoD流程学习与实践</div>
                        <div class="two-column">
                            <div class="card">
                                <div class="card-title">📚 DoD流程理解</div>
                                <div class="card-content">
                                    <ul>
                                        <li>深入学习公司Definition of Done流程</li>
                                        <li>理解从需求到交付的完整流程</li>
                                        <li>掌握<span class="highlight">21个</span>关键环节的标准要求</li>
                                    </ul>
                                </div>
                            </div>
                            <div class="card">
                                <div class="card-title">🔑 关键环节掌握</div>
                                <div class="card-content">
                                    <ul>
                                        <li>需求实例化：提前一个迭代输出需求文档</li>
                                        <li>需求澄清：迭代计划会与需求澄清会</li>
                                        <li>代码开发：代码走查、UT覆盖率、自测报告</li>
                                        <li>需求验收：基于验收准则验收</li>
                                    </ul>
                                </div>
                            </div>
                        </div>
                        <div class="section">
                            <div class="section-title">实践应用</div>
                            <div class="content-grid">
                                <div class="card">
                                    <div class="card-content">
                                        <p>在项目开发中严格按照DoD流程执行</p>
                                    </div>
                                </div>
                                <div class="card">
                                    <div class="card-content">
                                        <p>确保每个环节的交付物符合标准</p>
                                    </div>
                                </div>
                                <div class="card">
                                    <div class="card-content">
                                        <p>提升代码质量和交付效率</p>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>

                    <!-- 第9页：问题与思考 -->
                    <div class="slide" id="sec-think-arch">
                        <div class="slide-title">问题与思考</div>
                        <div class="two-column">
                            <div class="card">
                                <div class="section-title problem">⚠️ 核心问题</div>
                                <div class="card-content">
                                    <p><strong>2D/3D底层技术栈不统一</strong></p>
                                    <ul>
                                        <li>2D基于OpenLayers，3D基于Cesium</li>
                                        <li>缺乏统一的业务封装层</li>
                                        <li>接口设计不一致，难以统一</li>
                                    </ul>
                                    <p style="margin-top: 16px;"><strong>实际影响</strong></p>
                                    <ul>
                                        <li>维护成本高（需维护两套）</li>
                                        <li>学习成本高（其他团队需学两套）</li>
                                        <li>开发效率低（重复实现）</li>
                                    </ul>
                                </div>
                            </div>
                            <div class="card">
                                <div class="section-title solution">💡 解决思路</div>
                                <div class="card-content">
                                    <ul>
                                        <li><strong>统一封装层</strong>：在底层SDK之上构建统一业务抽象</li>
                                        <li><strong>适配器模式</strong>：通过适配器统一接口</li>
                                        <li><strong>渐进式重构</strong>：逐步统一，降低风险</li>
                                    </ul>
                                    <p style="margin-top: 16px;"><strong>预期价值</strong></p>
                                    <ul>
                                        <li>降低维护成本</li>
                                        <li>降低学习成本</li>
                                        <li>提升开发效率</li>
                                        <li>便于团队协作</li>
                                    </ul>
                                </div>
                            </div>
                        </div>
                    </div>

                    <!-- 第10页：测试问题与AI解决方案 -->
                    <div class="slide" id="sec-think-test">
                        <div class="slide-title">测试问题与AI解决方案</div>
                        <div class="two-column">
                            <div class="card">
                                <div class="section-title problem">⚠️ 实际工作中遇到的问题</div>
                                <div class="card-content">
                                    <ul>
                                        <li>先写代码再写测试用例，需要深入理解程序逻辑、接口设计、边界情况</li>
                                        <li>代码定制化，扩展需要重构，重构后测试用例失效</li>
                                    </ul>
                                    <p style="margin-top: 16px;"><strong>问题根源</strong></p>
                                    <ul>
                                        <li>测试用例依赖具体实现，而非接口契约</li>
                                        <li>重构时内部实现改变，测试用例失效</li>
                                    </ul>
                                </div>
                            </div>
                            <div class="card">
                                <div class="section-title solution">🤖 AI解决方案</div>
                                <div class="card-content">
                                    <ul>
                                        <li><strong>AI生成接口契约测试</strong>：快速为现有代码生成测试</li>
                                        <li><strong>AI重构测试用例</strong>：代码重构后，自动更新测试</li>
                                        <li><strong>AI分析覆盖率</strong>：识别测试盲点，生成补充测试</li>
                                    </ul>
                                    <p style="margin-top: 16px;"><strong>核心策略</strong></p>
                                    <ul>
                                        <li>测试接口契约，不测试具体实现</li>
                                        <li>重构时接口契约不变，测试有效</li>
                                    </ul>
                                </div>
                            </div>
                        </div>
                        <div class="section">
                            <div class="section-title solution">📊 实际效果</div>
                            <div class="content-grid">
                                <div class="card">
                                    <div class="card-content" style="text-align: center;">
                                        <div style="font-size: 36pt; font-weight: bold; color: #0285CA;">70%+</div>
                                        <p>测试生成效率提升</p>
                                    </div>
                                </div>
                                <div class="card">
                                    <div class="card-content" style="text-align: center;">
                                        <div style="font-size: 36pt; font-weight: bold; color: #0285CA;">80%+</div>
                                        <p>重构后测试更新效率提升</p>
                                    </div>
                                </div>
                                <div class="card">
                                    <div class="card-content" style="text-align: center;">
                                        <div style="font-size: 36pt; font-weight: bold; color: #0285CA;">显著</div>
                                        <p>测试可维护性提升</p>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>

                    <!-- 第11页：未来规划 -->
                    <div class="slide" id="sec-plan-future">
                        <div class="slide-title">未来规划</div>
                        <div class="section">
                            <div class="section-title">🎯 未来规划</div>
                            <div class="content-grid">
                                <div class="card">
                                    <div class="card-content">
                                        <p><strong>深耕WebGIS 3D</strong></p>
                                        <p>围绕通信可视化/运维场景，建立核心技术竞争力</p>
                                    </div>
                                </div>
                                <div class="card">
                                    <div class="card-content">
                                        <p><strong>承担重点任务</strong></p>
                                        <p>数字孪生平台架构设计与优化</p>
                                    </div>
                                </div>
                                <div class="card">
                                    <div class="card-content">
                                        <p><strong>建设模型处理能力</strong></p>
                                        <p>探索图像检测、实景建模等技术方向</p>
                                    </div>
                                </div>
                                <div class="card">
                                    <div class="card-content">
                                        <p><strong>6G时代技术支撑</strong></p>
                                        <p>为通信网络可视化和管理提供技术支撑，做好技术储备</p>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>

                    <!-- 第12页：总结与思考 -->
                    <div class="slide" id="sec-summary">
                        <div class="slide-title">总结与思考</div>
                        <div class="section">
                            <div class="section-title">💭 工作思考</div>
                            <div class="content-grid">
                                <div class="card">
                                    <div class="card-content">
                                        <p><strong>个人能力离不开平台</strong></p>
                                        <p>技术选型必须服务于业务需求</p>
                                    </div>
                                </div>
                                <div class="card">
                                    <div class="card-content">
                                        <p><strong>技术能力与业务需求深度结合</strong></p>
                                        <p>才能创造最大价值</p>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="section">
                            <div class="section-title">🚀 未来规划</div>
                            <div class="workflow">
                                <div class="workflow-item">
                                    <div class="workflow-number">1</div>
                                    <div class="workflow-content">深耕WebGIS 3D，围绕通信可视化/运维场景，建立核心技术竞争力</div>
                                </div>
                                <div class="arrow">↓</div>
                                <div class="workflow-item">
                                    <div class="workflow-number">2</div>
                                    <div class="workflow-content">承担数字孪生平台架构设计与优化等重点任务</div>
                                </div>
                                <div class="arrow">↓</div>
                                <div class="workflow-item">
                                    <div class="workflow-number">3</div>
                                    <div class="workflow-content">建设模型处理能力，探索图像检测、实景建模等技术方向</div>
                                </div>
                                <div class="arrow">↓</div>
                                <div class="workflow-item">
                                    <div class="workflow-number">4</div>
                                    <div class="workflow-content">为6G时代的通信网络可视化和管理提供技术支撑，做好技术储备</div>
                                </div>
                            </div>
                        </div>
                        <div class="stat-box" style="margin-top: 40px;">
                            <div style="font-size: 24pt; margin-bottom: 16px;">💡 核心价值</div>
                            <div style="font-size: 18pt;">技术选型服务于业务需求，为公司在6G时代的业务拓展提供技术支撑</div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <!-- 引入 ECharts -->
        <script src="https://cdn.jsdelivr.net/npm/echarts@5/dist/echarts.min.js"></script>

        <script>
            function scrollToSection(id) {
                const el = document.getElementById(id);
                if (!el) return;
                const container = document.querySelector('.main-content');
                if (!container) return;
                // 计算在主内容区域中的相对位置
                const top = el.offsetTop - 20;
                container.scrollTo({ top, behavior: 'smooth' });

                // 更新导航激活状态
                document.querySelectorAll('.nav-item').forEach((item) => {
                    if (item.getAttribute('data-target') === id) {
                        item.classList.add('active');
                    } else {
                        item.classList.remove('active');
                    }
                });
            }

            function toggleSidebar() {
                const sidebar = document.querySelector('.sidebar');
                const toggle = document.querySelector('.toc-toggle');
                if (!sidebar || !toggle) return;
                const collapsed = sidebar.classList.toggle('collapsed');
                toggle.textContent = collapsed ? '展开目录' : '收起目录';
            }

            function initCodeChart() {
                const dom = document.getElementById('codeChart');
                if (!dom || typeof echarts === 'undefined') return;
                const chart = echarts.init(dom);
                const option = {
                    title: {
                        text: '业务代码 & 测试代码分布',
                        left: 'center',
                        textStyle: { color: '#111827', fontSize: 16 }
                    },
                    tooltip: {
                        trigger: 'item',
                        formatter: '{a}<br/>{b}: {c} 行 ({d}%)'
                    },
                    legend: {
                        bottom: 0,
                        textStyle: { color: '#4B5563' }
                    },
                    series: [
                        {
                            name: '业务代码分布',
                            type: 'pie',
                            radius: '40%',
                            center: ['25%', '50%'],
                            data: [
                                { value: 9086, name: '其他工具类和模块', itemStyle: { color: '#0285CA' } },
                                { value: 2604, name: '3D瓦片管理器', itemStyle: { color: '#34D399' } },
                                { value: 1928, name: '视图核心类', itemStyle: { color: '#60A5FA' } },
                                { value: 1921, name: '栅格管理器', itemStyle: { color: '#A855F7' } },
                                { value: 1354, name: '矢量管理器', itemStyle: { color: '#F97316' } },
                                { value: 1046, name: '图层管理器', itemStyle: { color: '#EF4444' } }
                            ],
                            label: {
                                show: true,
                                formatter: '{b}\n{d}%'
                            }
                        },
                        {
                            name: '测试代码分布',
                            type: 'pie',
                            radius: '40%',
                            center: ['75%', '50%'],
                            data: [
                                { value: 25381, name: '其他测试文件', itemStyle: { color: '#3B82F6' } },
                                { value: 1763, name: '3D瓦片管理器测试', itemStyle: { color: '#22C55E' } },
                                { value: 1653, name: '小区管理器测试', itemStyle: { color: '#FACC15' } },
                                { value: 1258, name: '栅格管理器测试', itemStyle: { color: '#EC4899' } }
                            ],
                            label: {
                                show: true,
                                formatter: '{b}\n{d}%'
                            }
                        }
                    ]
                };
                chart.setOption(option);
                window.addEventListener('resize', () => {
                    chart.resize();
                });
            }

            // 初始滚动到总览并初始化图表
            document.addEventListener('DOMContentLoaded', () => {
                scrollToSection('sec-work-overview');
                initCodeChart();
            });
        </script>
    </body>
    </html>

