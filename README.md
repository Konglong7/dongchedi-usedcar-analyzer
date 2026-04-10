# 懂车帝二手车数据分析平台

基于 Python 爬虫 + Web 前端的懂车帝二手车数据采集与可视化分析系统。

## 功能特性

- **数据爬取**：自动抓取懂车帝二手车列表数据，包含价格、里程、品牌、年份等信息
- **字体反爬破解**：通过字体渲染 + 结构特征识别技术，解密懂车帝自定义字体加密
- **数据可视化**：价格分布图、品牌占比饼图、年份分布折线图、价格-里程散点图
- **智能筛选**：支持按价格区间、品牌、年份、城市等多维度筛选
- **详情直达**：每辆车提供详情页链接，方便快速查看

## 在线访问

**在线访问：** [https://konglong7.github.io/dongchedi-usedcar-analyzer/](https://konglong7.github.io/dongchedi-usedcar-analyzer/)

## 技术栈

| 模块 | 技术 |
|------|------|
| 爬虫 | Python + requests + fontTools + Pillow |
| 字体解密 | CFF字体解析 + 图像渲染 + 孔洞特征识别 + NCC匹配 |
| 前端 | HTML + CSS + ECharts |
| 部署 | 静态文件托管 |

## 快速开始

### 1. 安装依赖

```bash
pip install requests fonttools Pillow numpy brotli
```

### 2. 运行爬虫

```bash
python scraper.py
```

爬虫会自动生成 `data.json` 和 `data.js` 文件。

### 3. 启动前端

```bash
# 方式一：Python 内置服务器
python -m http.server 8080

# 方式二：直接用浏览器打开 index.html（需要 data.js 在同目录）
```

访问 `http://localhost:8080` 即可查看数据面板。

## 数据字段说明

| 字段 | 说明 |
|------|------|
| name | 车辆名称（如：奥迪A5 2016款 Sportback） |
| year | 生产年份 |
| brand | 品牌 |
| series | 车系 |
| mileage | 里程数（如：11.29万公里） |
| location | 所在城市 |
| transfer | 过户次数 |
| price | 二手车价格（万元） |
| guide_price | 新车指导价（万元） |
| detail_url | 懂车帝详情页链接 |

## 字体反爬破解原理

懂车帝使用自定义 Web Font 对价格等敏感字段进行加密：

1. API 返回的 `x-tt-zhal` 响应头包含字体文件 URL
2. 价格字段中的数字被替换为 Unicode 私用区字符（U+E000-U+F8FF）
3. 每次请求使用不同的字体文件，字符映射动态变化

本项目采用三层解密策略：

1. **年份映射**：利用 API 返回的未加密 `car_year` 字段，与加密的 `sub_title` 年份部分对比，使用多数投票法构建基础映射
2. **结构特征识别**：渲染字体字符，通过孔洞检测、重心位置等特征确定性识别数字 0/6/8/9
3. **NCC 精细匹配**：对剩余字符使用归一化互相关与系统字体参考数字对比

## 项目结构

```
├── scraper.py          # 爬虫脚本（含字体解密逻辑）
├── index.html          # 前端可视化页面
├── data.js             # 爬虫生成的数据文件（前端直接加载）
├── data.json           # 爬虫生成的原始 JSON 数据
├── .gitignore
└── README.md
```

## 注意事项

- 本项目仅供学习交流使用，请勿用于商业用途
- 爬取频率已做限速处理（每页间隔1秒），请合理使用
- 字体映射每次请求不同，`data.js` 需要定期重新生成以获取最新数据

## License

MIT
