digraph STYLE_GUIDE {
    // 我们的流程 DSL 的风格指南，用 DSL 本身编写

    // 节点类型示例及其形状
    subgraph cluster_node_types {
        label="节点类型与形状";

        // 问题是菱形
        "这是问题吗?" [shape=diamond];

        // 操作是方框（默认）
        "执行操作" [shape=box];

        // 命令是纯文本
        "git commit -m 'msg'" [shape=plaintext];

        // 状态是椭圆
        "当前状态" [shape=ellipse];

        // 警告是八边形
        "停止: 严重警告" [shape=octagon, style=filled, fillcolor=red, fontcolor=white];

        // 入口/出口是双圆圈
        "流程开始" [shape=doublecircle];
        "流程完成" [shape=doublecircle];

        // 各类型示例
        "测试通过了吗?" [shape=diamond];
        "先写测试" [shape=box];
        "npm test" [shape=plaintext];
        "我卡住了" [shape=ellipse];
        "永远不要使用 git add -A" [shape=octagon, style=filled, fillcolor=red, fontcolor=white];
    }

    // 边命名约定
    subgraph cluster_edge_types {
        label="边标签";

        "二元决策?" [shape=diamond];
        "是路径" [shape=box];
        "否路径" [shape=box];

        "二元决策?" -> "是路径" [label="是"];
        "二元决策?" -> "否路径" [label="否"];

        "多选?" [shape=diamond];
        "选项 A" [shape=box];
        "选项 B" [shape=box];
        "选项 C" [shape=box];

        "多选?" -> "选项 A" [label="条件 A"];
        "多选?" -> "选项 B" [label="条件 B"];
        "多选?" -> "选项 C" [label="其他情况"];

        "流程 A 完成" [shape=doublecircle];
        "流程 B 开始" [shape=doublecircle];

        "流程 A 完成" -> "流程 B 开始" [label="触发", style=dotted];
    }

    // 命名模式
    subgraph cluster_naming_patterns {
        label="命名模式";

        // 问题以 ? 结尾
        "我应该做 X 吗?";
        "这可以是 Y 吗?";
        "Z 是真的吗?";
        "我完成 W 了吗?";

        // 操作以动词开头
        "编写测试";
        "搜索模式";
        "提交变更";
        "寻求帮助";

        // 命令是字面量
        "grep -r 'pattern' .";
        "git status";
        "npm run build";

        // 状态描述情况
        "测试失败";
        "构建完成";
        "卡在错误上";
    }

    // 流程结构模板
    subgraph cluster_structure {
        label="流程结构模板";

        "触发: 某事发生" [shape=ellipse];
        "初始检查?" [shape=diamond];
        "主要操作" [shape=box];
        "git status" [shape=plaintext];
        "再次检查?" [shape=diamond];
        "替代操作" [shape=box];
        "停止: 不要这样做" [shape=octagon, style=filled, fillcolor=red, fontcolor=white];
        "流程完成" [shape=doublecircle];

        "触发: 某事发生" -> "初始检查?";
        "初始检查?" -> "主要操作" [label="是"];
        "初始检查?" -> "替代操作" [label="否"];
        "主要操作" -> "git status";
        "git status" -> "再次检查?";
        "再次检查?" -> "流程完成" [label="正常"];
        "再次检查?" -> "停止: 不要这样做" [label="有问题"];
        "替代操作" -> "流程完成";
    }

    // 何时使用哪种形状
    subgraph cluster_shape_rules {
        label="何时使用各形状";

        "选择形状" [shape=ellipse];

        "是决策?" [shape=diamond];
        "使用菱形" [shape=diamond, style=filled, fillcolor=lightblue];

        "是命令?" [shape=diamond];
        "使用纯文本" [shape=plaintext, style=filled, fillcolor=lightgray];

        "是警告?" [shape=diamond];
        "使用八边形" [shape=octagon, style=filled, fillcolor=pink];

        "是入口/出口?" [shape=diamond];
        "使用双圆圈" [shape=doublecircle, style=filled, fillcolor=lightgreen];

        "是状态?" [shape=diamond];
        "使用椭圆" [shape=ellipse, style=filled, fillcolor=lightyellow];

        "默认: 使用方框" [shape=box, style=filled, fillcolor=lightcyan];

        "选择形状" -> "是决策?";
        "是决策?" -> "使用菱形" [label="是"];
        "是决策?" -> "是命令?" [label="否"];
        "是命令?" -> "使用纯文本" [label="是"];
        "是命令?" -> "是警告?" [label="否"];
        "是警告?" -> "使用八边形" [label="是"];
        "是警告?" -> "是入口/出口?" [label="否"];
        "是入口/出口?" -> "使用双圆圈" [label="是"];
        "是入口/出口?" -> "是状态?" [label="否"];
        "是状态?" -> "使用椭圆" [label="是"];
        "是状态?" -> "默认: 使用方框" [label="否"];
    }

    // 好与坏的示例对比
    subgraph cluster_examples {
        label="好与坏的示例对比";

        // 好的：具体且形状正确
        "测试失败" [shape=ellipse];
        "阅读错误信息" [shape=box];
        "能复现吗?" [shape=diamond];
        "git diff HEAD~1" [shape=plaintext];
        "永远不要忽略错误" [shape=octagon, style=filled, fillcolor=red, fontcolor=white];

        "测试失败" -> "阅读错误信息";
        "阅读错误信息" -> "能复现吗?";
        "能复现吗?" -> "git diff HEAD~1" [label="是"];

        // 坏的：模糊且形状错误
        bad_1 [label="有问题", shape=box];  // 应该是椭圆（状态）
        bad_2 [label="修复它", shape=box];  // 太模糊
        bad_3 [label="检查", shape=box];  // 应该是菱形
        bad_4 [label="运行命令", shape=box];  // 应该是纯文本并带实际命令

        bad_1 -> bad_2;
        bad_2 -> bad_3;
        bad_3 -> bad_4;
    }
}
