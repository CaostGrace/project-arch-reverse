# HarmonyOS Next 项目架构模板

本文档为 HarmonyOS Next 项目提供详细的架构文档模板。

## HarmonyOS Next 特定章节

### H1. 技术栈详情

| 类别 | 技术 | 版本 | 说明 |
|------|------|------|------|
| 编程语言 | ArkTS | 4.x | 鸿蒙原生开发语言 |
| UI 框架 | ArkUI | 4.x | 声明式UI框架 |
| 构建工具 | Hvigor | 4.x | 鸿蒙构建工具 |
| 包管理 | OHPM | 1.x | OpenHarmony Package Manager |
| 状态管理 | AppStorage / LocalStorage | - | 响应式状态管理 |
| 网络 | HttpClient / Net | - | 网络请求 |
| 本地存储 | Preferences / SQLite | - | 本地数据持久化 |
| 分布式能力 | Distributed SoftBus | - | 分布式软总线 |
| 测试 | HarmonyOS Test Framework | - | 测试框架 |

### H2. 架构模式

#### 三层架构（官方推荐）
```
┌─────────────────────────────────────────┐
│         产品定制层 (Product)            │
│  ┌─────────────────────────────────┐    │
│  │  Entry类型HAP                   │    │
│  │  设备形态适配、响应式布局        │    │
│  │  窗口模式：全屏/分屏/悬浮/自由窗  │    │
│  └─────────────────────────────────┘    │
└─────────────────────────────────────────┘
                    │
┌─────────────────────────────────────────┐
│          特性层 (Feature)               │
│  ┌─────────────────────────────────┐    │
│  │  Feature类型HAP                 │    │
│  │  业务逻辑、UseCase              │    │
│  │  UI组件组合（无布局）            │    │
│  └─────────────────────────────────┘    │
└─────────────────────────────────────────┘
                    │
┌─────────────────────────────────────────┐
│          共用层 (Common)                │
│  ┌─────────────────────────────────┐    │
│  │  Library/HAR/HSP                │    │
│  │  网络、数据、工具类、通用组件    │    │
│  │  零形态感知（无设备相关代码）     │    │
│  └─────────────────────────────────┘    │
└─────────────────────────────────────────┘
```

### H3. 包类型选择指南

| 包类型 | 适用场景 | 特点 | 注意事项 |
|--------|----------|------|----------|
| **HAP** | 核心业务模块、应用入口 | 支持动态加载，可独立安装 | Entry类型必须有一个 |
| **HAR** | 静态共享资源、工具类、UI组件 | 编译时集成，适合发布到OHPM仓库 | 多包引用会增大体积，建议开启混淆 |
| **HSP** | 动态共享包、按需加载功能 | 运行时复用，减小包体积 | 仅支持应用内共享，HAR依赖HSP只能内部使用 |

### H4. 工程目录结构

```
MyApp (工程根目录)
├── AppScope/                     # 应用级配置和资源（自动生成，不可修改）
│   ├── app.json5                # 应用级配置（包名、版本、图标等）
│   └── resources/               # 应用级资源（全局资源）
├── common/                      # 共用层（Library/HAR/HSP）
│   ├── src/main/ets/
│   │   ├── network/             # 网络请求封装
│   │   ├── repository/          # 数据仓库
│   │   ├── model/               # 数据模型
│   │   ├── utils/               # 工具类
│   │   └── components/          # 通用组件（不含布局）
│   └── ohos.build-profile.json5
├── features/                    # 特性层（Feature HAP）
│   ├── login/                   # 登录模块
│   ├── search/                  # 搜索模块
│   └── payment/                 # 支付模块
├── products/                    # 产品定制层（Entry HAP）
│   ├── phone/                   # 手机形态
│   ├── tablet/                  # 平板形态
│   ├── pc/                      # PC形态
│   └── watch/                   # 穿戴设备形态
├── hvigorfile.ts                # 自定义构建脚本
├── build-profile.json5          # 签名和产品配置
└── oh-package.json5             # 依赖库信息
```

### H5. 配置文件分析

#### app.json5（应用级配置）
```json
{
  "app": {
    "bundleName": "com.example.myapp",
    "vendor": "example",
    "version": {
      "code": 10000,
      "name": "1.0.0"
    },
    "icon": "$media:app_icon",
    "label": "$string:app_name"
  }
}
```

#### module.json5（模块配置）
```json
{
  "module": {
    "name": "entry",
    "type": "entry",
    "deviceTypes": ["phone", "tablet"],
    "abilities": [
      {
        "name": "EntryAbility",
        "srcEntrance": "./ets/entryability/EntryAbility.ets",
        "description": "$string:entry_ability_desc",
        "icon": "$media:icon",
        "label": "$string:entry_ability_label",
        "startWindowIcon": "$media:icon",
        "startWindowBackground": "$color:start_window_background"
      }
    ]
  }
}
```

### H6. 分布式能力

#### 分布式软总线
```typescript
// 设备发现
import distributedDeviceManager from '@ohos.distributedDeviceManager';

const dm = distributedDeviceManager.createDistributedDeviceManager();
const devices = await dm.getAvailableDeviceList();
```

#### 分布式数据管理
```typescript
// 跨设备数据同步
import distributedDataObject from '@ohos.data.distributedDataObject';

const kvManager = distributedDataObject.createKVManager();
const kvStore = await kvManager.getKVStore('my_store');
await kvStore.put('key', 'value');
```

### H7. 响应式布局

#### 断点布局
```typescript
@Entry
@Component
struct MyPage {
  build() {
    Column() {
      if (deviceInfo.screenWidth > 800) {
        // 大屏布局
        Row() {
          SideBar()
          MainContent()
        }
      } else {
        // 小屏布局
        Column() {
          Header()
          MainContent()
        }
      }
    }
  }
}
```

#### 自适应布局
```typescript
@Entry
@Component
struct AdaptiveLayout {
  build() {
    Grid() {
      ForEach(this.data, (item) => {
        GridItem() {
          Card({ data: item })
        }
      })
    }
    .columnsTemplate(deviceInfo.screenWidth > 600 ? '1fr 1fr 1fr' : '1fr 1fr')
    .columnsGap(16)
    .rowsGap(16)
  }
}
```

### H8. 状态管理

#### AppStorage（全局状态）
```typescript
// 定义全局状态
AppStorage.setOrCreate('userName', '');

// 组件中使用
@Entry
@Component
struct UserProfile {
  @StorageProp('userName') userName: string = '';
  
  build() {
    Text(`Hello, ${this.userName}`)
  }
}
```

#### LocalStorage（页面状态）
```typescript
const localStorage = new LocalStorage();

@Entry(localStorage)
@Component
struct MyPage {
  @LocalStorageProp('count') count: number = 0;
  
  build() {
    Button(`Count: ${this.count}`)
      .onClick(() => this.count++)
  }
}
```

### H9. 网络层设计

```typescript
// HttpClient封装
export class ApiService {
  private baseUrl: string = 'https://api.example.com';
  
  async get<T>(path: string, params?: Record<string, string>): Promise<T> {
    const httpClient = new HttpClient();
    const request = new HttpRequestInfo();
    request.url = `${this.baseUrl}${path}`;
    request.method = HttpMethod.GET;
    
    if (params) {
      request.parameters = params;
    }
    
    const response = await httpClient.request(request);
    return await response.json();
  }
  
  async post<T>(path: string, body: Record<string, unknown>): Promise<T> {
    const httpClient = new HttpClient();
    const request = new HttpRequestInfo();
    request.url = `${this.baseUrl}${path}`;
    request.method = HttpMethod.POST;
    request.body = JSON.stringify(body);
    
    const response = await httpClient.request(request);
    return await response.json();
  }
}
```

### H10. 本地存储

#### Preferences
```typescript
import preferences from '@ohos.data.preferences';

async function saveToken(token: string): Promise<void> {
  const pref = await preferences.getPreferences(getContext(), 'user_prefs');
  await pref.put('token', token);
  await pref.flush();
}

async function getToken(): Promise<string | undefined> {
  const pref = await preferences.getPreferences(getContext(), 'user_prefs');
  return pref.get('token', '') as string;
}
```

## 标准章节参考

详见 [template.md](./template.md) 中的标准章节结构。

## 最佳实践建议

### 依赖管理
- 遵循单向依赖原则：共用层 → 特性层 → 产品定制层
- HAR/HSP不支持循环依赖，配置时需注意
- 通过oh-package.json5区分必需模块与可选模块

### 资源管理
- AppScope资源与Module资源同名时，编译后只保留AppScope的
- 建议按功能模块组织资源文件

### 性能优化
- 优先使用HSP减少包体积
- HAR包建议开启混淆保护代码
- 合理使用懒加载和按需加载

### 分布式适配
- 跨设备场景需提前封装分布式接口
- 注意分布式数据同步的一致性问题
