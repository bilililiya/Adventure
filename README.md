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
│   │   │       ├── ui/                         # 界面 & UI
│   │   │       │   ├── HUDUI.java              # 血条、魔法条、经验条
│   │   │       │   ├── MenuUI.java             # 主菜单
│   │   │       │   └── CharacterSelectUI.java  # 角色选择界面
|   |   |       |   └──GameOverUI.java          #游戏结束
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
-地图接口
public interface Map {
    //加载指定地图
    void loadMap(String mapName);
    //渲染地图
    void render();

    //获取地图长度
    int getLength();
    //获取地图高度
    int getHeight();

    //在地图上生成一个掉落物（血包、经验值）
    void drop(Item item);

    //获取当前位置
    int[] getCurrentLocation();
    //显示当前位置（角色x,y坐标）
    void displayCurrentLocation();
    //移动到指定位置
    void moveTo(String location);
    //判断某个位置是否在地图中
    boolean hasLocation(String location);

    //显示整个地图的基本信息
    void displayMapOverView();
}

-道具接口
public interface Item{
    //获得道具在地图中的横坐标
    int getX();
    //获取纵坐标
    int getY();
    
    //渲染道具
    void render();
    
    //道具被玩家拾取时触发
    void onPickup(Player player);
    
    //道具类型(血包、经验值等)
    String getType();
}



//道具类型(血包、经验值等)
String getType();
}



-敌人接口
package com.game.adventure.core.enemy;

public interface Enemy {
    // 基础属性获取
    int getMaxHealth();
    int getCurrentHealth();
    int getAttackPower();
    double getPositionX();
    double getPositionY();
    String getName();
    EnemyType getType();
    // 状态检查
    boolean isAlive();
    boolean isInCombat();
    boolean canAttack();
    // 战斗行为
    void takeDamage(int damage);
    int attack();
    void enterCombat();
    void exitCombat();
    // 移动相关
    void setPosition(double x, double y);
    void moveTowards(double targetX, double targetY);
    // 死亡处理
    List<Item> dropItems();
    void die();
    // 视觉表现
    Node getVisualNode();
    void updateVisual();
    // 生命周期
    void update(double deltaTime);
    void cleanup();

public enum EnemyType {
    SMALL_MONSTER("小怪兽", 1),
    BOSS("Boss", 2);
    private final String displayName;
    private final int level;
    EnemyType(String displayName, int level) {
        this.displayName = displayName;
        this.level = level;
    }
    public String getDisplayName() { return displayName; }
    public int getLevel() { return level; }
}
// 2. 敌人类型枚举
public enum EnemyType {
    SMALL_MONSTER("小怪兽", 1),
    BOSS("Boss", 2);
    private final String displayName;
    private final int level;
    EnemyType(String displayName, int level) {
        this.displayName = displayName;
        this.level = level;
    }
    public String getDisplayName() { return displayName; }
    public int getLevel() { return level; }
}

// 3. 敌人状态枚举
public enum EnemyState {
    IDLE,       // 待机状态
    PATROL,     // 巡逻状态  
    COMBAT,     // 战斗状态
    ATTACKING,  // 攻击状态
    DAMAGED,    // 受伤状态
    DYING,      // 死亡中
    DEAD        // 已死亡
}
// 4. 抽象敌人基
public abstract class AbstractEnemy implements Enemy {
    protected String name;
    protected int maxHealth;
    protected int currentHealth;
    protected int attackPower;
    protected double positionX;
    protected double positionY;
    protected EnemyType type;
    protected EnemyState state;
    // JavaFX视觉组件
    protected Rectangle visualNode;
    protected double width = 30;
    protected double height = 30;
    // 战斗相关
    protected boolean inCombat = false;
    protected long lastAttackTime = 0;
    protected long attackCooldown = 1000; // 1秒攻击间隔
    // 掉落相关
    protected Random random = new Random();
    protected double healthPotionDropRate = 0.3;  // 30%掉落回血剂
    protected double expGemDropRate = 0.4;        // 40%掉落经验宝石
    
    public AbstractEnemy(String name, int maxHealth, int attackPower, double x, double y, EnemyType type) {
        this.name = name;
        this.maxHealth = maxHealth;
        this.currentHealth = maxHealth;
        this.attackPower = attackPower;
        this.positionX = x;
        this.positionY = y;
        this.type = type;
        this.state = EnemyState.IDLE;
        initVisualNode();
    }
    protected void initVisualNode() {
        visualNode = new Rectangle(width, height);
        visualNode.setFill(Color.RED);
        visualNode.setStroke(Color.DARKRED);
        visualNode.setStrokeWidth(2);
        visualNode.setX(positionX);
        visualNode.setY(positionY);
    }
    
    // 基础属性实现
    @Override
    public int getMaxHealth() { return maxHealth; }
    @Override
    public int getCurrentHealth() { return currentHealth; }
    @Override
    public int getAttackPower() { return attackPower; }
    @Override
    public double getPositionX() { return positionX; }
    @Override
    public double getPositionY() { return positionY; }
    @Override
    public String getName() { return name; }
    @Override
    public EnemyType getType() { return type; }
    // 状态检查实现
    @Override
    public boolean isAlive() { 
        return currentHealth > 0 && state != EnemyState.DEAD; 
    }
    @Override
    public boolean isInCombat() { 
        return inCombat && state == EnemyState.COMBAT; 
    }
    @Override
    public boolean canAttack() {
        return isAlive() && isInCombat() && 
               (System.currentTimeMillis() - lastAttackTime) >= attackCooldown;
    }
    
    // 战斗行为实现
    @Override
    public void takeDamage(int damage) {
        if (!isAlive()) return;
        
        currentHealth = Math.max(0, currentHealth - damage);
        state = EnemyState.DAMAGED;
        
        if (currentHealth <= 0) {
            die();
        } else {
            // 被攻击后进入战斗状态
            enterCombat();
        }
        updateVisual();
    }
    @Override
    public int attack() {
        if (!canAttack()) return 0;
        
        lastAttackTime = System.currentTimeMillis();
        state = EnemyState.ATTACKING;
        
        return attackPower;
    }
    @Override
    public void enterCombat() {
        if (isAlive()) {
            inCombat = true;
            state = EnemyState.COMBAT;
        }
    }
    @Override
    public void exitCombat() {
        inCombat = false;
        if (isAlive()) {
            state = EnemyState.IDLE;
        }
    }
    
    // 位置相关实现
    @Override
    public void setPosition(double x, double y) {
        this.positionX = x;
        this.positionY = y;
        updateVisualPosition();
    }
    @Override
    public void moveTowards(double targetX, double targetY) {
        // 简单的移动逻辑，子类可以重写
        double dx = targetX - positionX;
        double dy = targetY - positionY;
        double distance = Math.sqrt(dx * dx + dy * dy);
        
        if (distance > 1.0) {
            double moveSpeed = getMoveSpeed();
            positionX += (dx / distance) * moveSpeed;
            positionY += (dy / distance) * moveSpeed;
            updateVisualPosition();
        }
    }
    protected abstract double getMoveSpeed();
    
    // 死亡处理实现
    @Override
    public List<Item> dropItems() {
        List<Item> drops = new ArrayList<>();
        double dropChance = random.nextDouble();
        if (dropChance < healthPotionDropRate) {
            drops.add(new HealthPotion(positionX, positionY));
        } else if (dropChance < healthPotionDropRate + expGemDropRate) {
            drops.add(new ExpGem(positionX, positionY, getExpValue()));
        }
        // 否则什么都不掉落
        return drops;
    }
    protected abstract int getExpValue();
    @Override
    public void die() {
        state = EnemyState.DEAD;
        currentHealth = 0;
        inCombat = false;
        updateVisual();
    }
    // 视觉表现实现
    @Override
    public Node getVisualNode() {
        return visualNode;
    }
    @Override
    public void updateVisual() {
        if (visualNode == null) return;
        switch (state) {
            case DEAD:
                visualNode.setVisible(false);
                break;
            case DAMAGED:
                visualNode.setFill(Color.ORANGE);
                break;
            case COMBAT:
                visualNode.setFill(Color.DARKRED);
                break;
            case ATTACKING:
                visualNode.setFill(Color.YELLOW);
                break;
            default:
                visualNode.setFill(Color.RED);
                break;
        }
        // 血量条显示（简化版）
        double healthRatio = (double) currentHealth / maxHealth;
        visualNode.setOpacity(0.5 + 0.5 * healthRatio);
    }
    protected void updateVisualPosition() {
        if (visualNode != null) {
            visualNode.setX(positionX);
            visualNode.setY(positionY);
        }
    }
    // 生命周期实现
    @Override
    public void update(double deltaTime) {
        if (!isAlive()) return;
        // 状态机更新
        updateStateMachine(deltaTime);
        // 视觉更新
        updateVisual();
    }
    protected void updateStateMachine(double deltaTime) {
        // 基础状态机逻辑，子类可以重写
        switch (state) {
            case DAMAGED:
                // 受伤状态持续一段时间后回到战斗或待机状态
                if (System.currentTimeMillis() - lastAttackTime > 500) {
                    state = inCombat ? EnemyState.COMBAT : EnemyState.IDLE;
                }
                break;
            case ATTACKING:
                // 攻击动画结束后回到战斗状态
                if (System.currentTimeMillis() - lastAttackTime > 300) {
                    state = EnemyState.COMBAT;
                }
                break;
        }
    }
    @Override
    public void cleanup() {
        if (visualNode != null) {
            visualNode.setVisible(false);
        }
    }
}

// 5. 小怪兽实现类
public class SmallMonster extends AbstractEnemy {
    private static final int BASE_HEALTH = 50;
    private static final int BASE_ATTACK = 10;
    private static final double MOVE_SPEED = 1.0;
    public SmallMonster(double x, double y) {
        super("小怪兽", BASE_HEALTH, BASE_ATTACK, x, y, EnemyType.SMALL_MONSTER);
        
        // 小怪兽特有的视觉设置
        width = 25;
        height = 25;
        visualNode.setWidth(width);
        visualNode.setHeight(height);
        
        // 调整掉落率
        healthPotionDropRate = 0.25;
        expGemDropRate = 0.35;
    }
    @Override
    protected double getMoveSpeed() {
        return MOVE_SPEED;
    }
    @Override
    protected int getExpValue() {
        return 10 + random.nextInt(6); // 10-15经验值
    }
    @Override
    protected void updateStateMachine(double deltaTime) {
        super.updateStateMachine(deltaTime);
        
        // 小怪兽特有的AI逻辑可以在这里添加
        if (state == EnemyState.IDLE) {
            // 简单的巡逻逻辑
            if (random.nextDouble() < 0.01) { // 1%的概率改变状态
                state = EnemyState.PATROL;
            }
        }
    }
}
// 6. Boss怪物实现类
public class BossMonster extends AbstractEnemy {
    private static final int BASE_HEALTH = 200;
    private static final int BASE_ATTACK = 25;
    private static final double MOVE_SPEED = 0.5;
    private int level;
    
    public BossMonster(double x, double y, int level) {
        super("Boss怪物", 
              BASE_HEALTH + (level - 1) * 50,  // 每级增加50血
              BASE_ATTACK + (level - 1) * 5,   // 每级增加5攻击
              x, y, EnemyType.BOSS);
        this.level = level;
        // Boss特有的视觉设置
        width = 50;
        height = 50;
        visualNode.setWidth(width);
        visualNode.setHeight(height);
        visualNode.setFill(Color.PURPLE);
        visualNode.setStroke(Color.BLACK);
        visualNode.setStrokeWidth(3);
        
        // Boss掉落率更高
        healthPotionDropRate = 0.6;
        expGemDropRate = 0.8;
        attackCooldown = 800; // Boss攻击更频繁
    }
    @Override
    protected double getMoveSpeed() {
        return MOVE_SPEED;
    }
    @Override
    protected int getExpValue() {
        return 50 + level * 20 + random.nextInt(21); // 基础50 + 等级奖励 + 随机0-20
    }
    @Override
    public void updateVisual() {
        if (visualNode == null) return;
        switch (state) {
            case DEAD:
                visualNode.setVisible(false);
                break;
            case DAMAGED:
                visualNode.setFill(Color.ORANGE);
                break;
            case COMBAT:
                visualNode.setFill(Color.DARKMAGENTA);
                break;
            case ATTACKING:
                visualNode.setFill(Color.GOLD);
                break;
            default:
                visualNode.setFill(Color.PURPLE);
                break;
        }
        
        // Boss血量条更明显
        double healthRatio = (double) currentHealth / maxHealth;
        visualNode.setOpacity(0.7 + 0.3 * healthRatio);
    }
    @Override
    protected void updateStateMachine(double deltaTime) {
        super.updateStateMachine(deltaTime);
        
    private void useSpecialAbility() {
        // Boss特殊技能的占位方法，可以在后续开发中扩展
        System.out.println(name + " 使用了特殊技能!");
    }
    public int getLevel() {
        return level;
    }
}

// 7. 敌人工厂类
public class EnemyFactory {
    private static final Random random = new Random();
    public static SmallMonster createRandomSmallMonster(double x, double y) {
        return new SmallMonster(x, y);
    }
    public static BossMonster createBoss(double x, double y, int level) {
        return new BossMonster(x, y, level);
    }
    public static Enemy createEnemyForProgress(double x, double y, int mapProgress, boolean shouldBeBoss) {
        if (shouldBeBoss) {
            int bossLevel = Math.max(1, mapProgress / 10); // 每10进度增加1级Boss
            return createBoss(x, y, bossLevel);
        } else {
            return createRandomSmallMonster(x, y);
        }
    }
    //批量创建敌人
    public static Enemy[] createEnemies(int count, double minX, double maxX, double minY, double maxY, int mapProgress) {
        Enemy[] enemies = new Enemy[count];
        for (int i = 0; i < count - 1; i++) {
            double x = minX + random.nextDouble() * (maxX - minX);
            double y = minY + random.nextDouble() * (maxY - minY);
            enemies[i] = createRandomSmallMonster(x, y);
        }
        // 最后一个是Boss
        if (count > 0) {
            double x = minX + random.nextDouble() * (maxX - minX);
            double y = minY + random.nextDouble() * (maxY - minY);
            enemies[count - 1] = createEnemyForProgress(x, y, mapProgress, true);
        }
        return enemies;
    }
}

// 8. 敌人管理器类--负责敌人的统一管理和更新
public class EnemyManager {
    private List<Enemy> enemies;
    private Group enemyGroup;
    private Random random;
    // 生成相关参数
    private double lastSpawnTime = 0;
    private double spawnInterval = 3000; // 3秒生成一个敌人
    private int maxEnemies = 20;
    private int mapProgress = 0;
    
    public EnemyManager(Group enemyGroup) {
        this.enemies = new ArrayList<>();
        this.enemyGroup = enemyGroup;
        this.random = new Random();
    }
    public void addEnemy(Enemy enemy) {
        if (enemy != null) {
            enemies.add(enemy);
            enemyGroup.getChildren().add(enemy.getVisualNode());
        }
    }
    public void removeEnemy(Enemy enemy) {
        if (enemy != null) {
            enemies.remove(enemy);
            enemyGroup.getChildren().remove(enemy.getVisualNode());
            enemy.cleanup();
        }
    }
    public void spawnRandomEnemy(double x, double y) {
        if (enemies.size() >= maxEnemies) return;
        boolean shouldBeBoss = random.nextDouble() < 0.1; // 10%概率生成Boss
        Enemy newEnemy = EnemyFactory.createEnemyForProgress(x, y, mapProgress, shouldBeBoss);
        addEnemy(newEnemy);
    }
    public void updateEnemies(double deltaTime) {
        // 更新现有敌人
        List<Enemy> toRemove = new ArrayList<>();
        for (Enemy enemy : enemies) {
            if (enemy.isAlive()) {
                enemy.update(deltaTime);
            } else {
                toRemove.add(enemy);
            }
        }
        // 移除死亡的敌人
        for (Enemy deadEnemy : toRemove) {
            removeEnemy(deadEnemy);
        }
        // 根据进度自动生成敌人
        autoSpawnEnemies();
    }
    private void autoSpawnEnemies() {
        double currentTime = System.currentTimeMillis();
        if (currentTime - lastSpawnTime > spawnInterval && enemies.size() < maxEnemies) {
            // 随机位置生成敌人
            double x = 50 + random.nextDouble() * 700;
            double y = 50 + random.nextDouble() * 500;
            spawnRandomEnemy(x, y);
            lastSpawnTime = currentTime;
        }
    }
    
-ui接口
//1. 主菜单界面接口
public interface MenuUI {
    // 初始化主菜单
    void initMenu();

    // 渲染主菜单,绘制“单人游戏”等界面元素
    void render();

    // 鼠标点击事件
    void handleClick(int mouseX, int mouseY);
}

//2. 角色选择界面接口
public interface CharacterSelectUI {
    // 初始化角色选择界面
    void initCharacterSelect();

    // 渲染角色选择界面
    void render();
}

//3. 战斗信息显示界面接口
public interface HUDUI {
    // 初始化HUD，加载血条、魔法条、经验条
    void initHUD(Player player);

    // 渲染HUD，绘制生命值，魔法值，经验条等战斗信息
    void render();

    // 更新生命值显示
    void updateHP(int currentHP, int maxHP);

    // 更新魔法值显示
    void updateMP(int currentMP, int maxMP);

    // 更新经验值显示
    void updateExp(int currentExp, int maxExp);
}

//4. 游戏结束界面接口
public interface GameOverUI {
    // 初始化游戏结束界面
    void initGameOver(boolean isWin);

    // 渲染游戏结束界面
    void render();

    // 判断是否点击“重新开始”按钮
    boolean isRestartClicked();

    // 判断是否点击“返回主菜单”按钮
    boolean isReturnMenuClicked();

    // 检查是否需要关闭游戏结束界面
    boolean needClose();
}
