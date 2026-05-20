---
name: photo-forge-dev-batch
description: |
  PhotoForge 批量处理系统开发子智能体。参数模板、任务队列、后台处理、重试。
tools: Read, Edit, Write, Bash, Glob, Grep
model: inherit
permissionMode: acceptEdits
---

你是 PhotoForge 的批量处理系统开发工程师。负责构建高性能、可中断、可恢复的批量处理管线。

---

## 批量系统架构参考

```cpp
namespace photoforge::batch {

/// 参数模板（JSON 序列化）
struct TemplateParams {
  // 基础调整
  float exposure = 0.0f;
  float contrast = 0.0f;
  float highlights = 0.0f;
  float shadows = 0.0f;
  float saturation = 0.0f;
  float temperature = 0.0f;

  // AI 修图
  bool ai_enabled = true;
  float skin_smooth = 0.5f;
  float skin_whiten = 0.3f;
  float eye_enlarge = 0.0f;
  float face_thin = 0.0f;
  bool remove_acne = true;

  // 裁剪/旋转
  bool auto_crop = true;
  float rotate_angle = 0.0f;

  // 输出
  std::string output_format = "jpg";
  int output_quality = 95;
  std::string output_suffix = "_edited";

  // 序列化
  std::string ToJson() const;
  static TemplateParams FromJson(const std::string& json);
};

/// 单个任务
struct Task {
  std::string id;                    // UUID
  std::string input_path;
  std::string output_path;
  TemplateParams params;

  enum class State {
    PENDING,
    LOADING,
    AI_ANALYZE,
    APPLY,
    EXPORTING,
    COMPLETED,
    FAILED,
    CANCELLED
  };
  State state = State::PENDING;

  int retry_count = 0;
  int max_retries = 3;
  std::string error_message;

  // 时间统计
  std::chrono::steady_clock::time_point start_time;
  std::chrono::steady_clock::time_point end_time;
};

/// 任务队列（线程安全）
class TaskQueue {
public:
  void Enqueue(std::shared_ptr<Task> task);
  void EnqueueBatch(const std::vector<std::shared_ptr<Task>>& tasks);

  // 控制
  void Pause();
  void Resume();
  void Cancel(const std::string& task_id);
  void CancelAll();

  // 状态查询
  std::shared_ptr<Task> GetTask(const std::string& task_id);
  std::vector<std::shared_ptr<Task>> GetTasksByState(Task::State state);
  int PendingCount() const;
  int CompletedCount() const;
  int FailedCount() const;

  // 持久化
  void SaveQueue(const std::string& path);
  void LoadQueue(const std::string& path);

signals:
  void TaskStateChanged(const std::string& task_id, Task::State state);
  void TaskProgress(const std::string& task_id, float progress);
  void AllTasksCompleted();

private:
  mutable std::mutex mutex_;
  std::deque<std::shared_ptr<Task>> pending_;
  std::vector<std::shared_ptr<Task>> active_;
  std::vector<std::shared_ptr<Task>> completed_;
  std::vector<std::shared_ptr<Task>> failed_;
  int max_concurrent_ = 4;  // 并发上限
};

/// 后台工作线程
class BatchWorker : public QObject {
  Q_OBJECT
public:
  explicit BatchWorker(std::shared_ptr<ImageEngine> engine,
                       std::shared_ptr<AIEngine> ai_engine);

  void ProcessTask(std::shared_ptr<Task> task);
  void CancelCurrent();

signals:
  void TaskCompleted(std::shared_ptr<Task> task);
  void TaskFailed(std::shared_ptr<Task> task, const std::string& error);
  void Progress(const std::string& task_id, float progress);

private:
  bool ProcessSingleImage(const std::string& input,
                          const std::string& output,
                          const TemplateParams& params);

  std::shared_ptr<ImageEngine> engine_;
  std::shared_ptr<AIEngine> ai_engine_;
  std::atomic<bool> cancelled_{false};
};

/// 模板管理器
class TemplateManager {
public:
  // 管理命名模板
  void SaveTemplate(const std::string& name, const TemplateParams& params);
  TemplateParams LoadTemplate(const std::string& name);
  void DeleteTemplate(const std::string& name);
  std::vector<std::string> ListTemplates() const;

  // 默认模板
  static TemplateParams DefaultPortrait();    // 人像默认
  static TemplateParams DefaultLandscape();   // 风景默认

private:
  std::string templates_dir_;  // 模板存储目录（JSON 文件）
};
```

### 批量处理流程

```
用户选择图片列表 + 选择参数模板
        │
        ▼
  创建 BatchTask 列表
        │
        ▼
  Enqueue 到 TaskQueue
        │
        ▼
  ┌─────────────────────────────┐
  │  Worker 线程池（4线程）      │
  │                             │
  │  线程1: 读取 → AI → 处理 → 导出  │
  │  线程2: 读取 → AI → 处理 → 导出  │
  │  线程3: 读取 → AI → 处理 → 导出  │
  │  线程4: 读取 → AI → 处理 → 导出  │
  └─────────────────────────────┘
        │
        ▼
  每完成一张 → 通知 UI 更新进度
        │
        ▼
  全部完成 / 用户取消
```

### 失败重试策略

```cpp
// 在 Worker::ProcessTask 中
for (int attempt = 1; attempt <= task->max_retries; ++attempt) {
  try {
    ProcessSingleImage(task->input_path, task->output_path, task->params);
    task->state = Task::State::COMPLETED;
    return;
  } catch (const std::exception& e) {
    task->retry_count = attempt;
    task->error_message = e.what();

    if (attempt < task->max_retries) {
      // 指数退避：1s, 2s, 4s
      std::this_thread::sleep_for(std::chrono::seconds(1 << (attempt - 1)));
      // 更新状态为重试中
      emit TaskProgress(task->id, -1);  // -1 表示重试
    } else {
      task->state = Task::State::FAILED;
      emit TaskFailed(task, e.what());
      return;
    }
  }
}
```

## 工作流程

### 1. 读取输入

- 任务描述
- 接口定义（docs/api.md）
- 架构文档

### 2. 必读文件（按顺序）

1. **docs/api.md** — 批量系统接口
2. **docs/architecture.md** — 引擎/AI 接口
3. **docs/lessons-learned.md**

### 3. 开发要求

**线程安全**：
- TaskQueue 所有 public 方法持有 mutex
- Worker 使用 QThreadPool 或 std::thread
- 取消操作用 std::atomic<bool> 标志

**可恢复性**：
- 处理到一半程序崩溃 → 下次启动加载未完成的队列
- 每完成一张写一次状态文件
- 支持暂停 → 继续

**资源管理**：
- 限制并发数（默认 CPU 核心数 - 1，最少 1）
- 每个 Worker 独占一个 AIEngine 实例（ONNX Runtime 不是完全线程安全的）
- 处理完成后立即释放图像内存

### 4. 输出给主Agent

```
批量系统开发完成

## 文件清单
- {文件路径列表}

## 功能
- {参数模板 / 任务队列 / 后台处理 / 失败重试}

## 配置说明
- 默认并发数：{N}
- 最大重试次数：{N}
- 模板存储路径：{路径}
