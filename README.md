# Adventure Game项目结构与接口设计

##项目结构
Adventure/                # 项目根目录
├── pom.xml               # Maven 配置文件
├── README.md             # 项目说明 & 接口定义
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/adventure/
│   │   │       ├── Main.java          # 程序入口
│   │   │       │
│   │   │       ├── core/              # 游戏核心框架
│   │   │       │   ├── GameLoop.java      # 游戏主循环
│   │   │       │   ├── GameObject.java    # 游戏对象基类（接口/抽象类）
│   │   │       │   ├── Updatable.java     # 可更新接口
│   │   │       │   └── Renderable.java    # 可渲染接口
│   │   │       │
│   │   │       ├── entity/            # 游戏实体（角色/怪物/子弹等）
│   │   │       │   ├── Player.java
│   │   │       │   ├── Enemy.java
│   │   │       │   ├── Bullet.java
│   │   │       │   └── EnemySpawner.java
│   │   │       │
│   │   │       ├── system/            # 系统功能模块
│   │   │       │   ├── Collision.java     # 碰撞检测
│   │   │       │   ├── InputHandler.java  # 键盘输入
│   │   │       │   └── LevelSystem.java   # 升级/经验系统
│   │   │       │
│   │   │       ├── ui/                # 界面 & UI
│   │   │       │   ├── HUD.java           # 血条/经验条
│   │   │       │   ├── Menu.java          # 主菜单
│   │   │       │   └── GameOverScreen.java
│   │   │       │
│   │   │       └── map/               # 地图 & 视角
│   │   │           ├── GameMap.java
│   │   │           └── Camera.java
│   │   │
│   │   └── resources/                 # 资源文件
│   │       ├── images/                # 图片（角色、怪物、地图）
│   │       ├── sounds/                # 音效
│   │       └── fxml/                  # 界面（如果用 FXML）
│   │
│   └── test/                          # 单元测试
│       └── java/
│           └── com/adventure/
│               └── PlayerTest.java


##接口设计
