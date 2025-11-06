flowchart TD
    %% 起始
    A[读取 Excel/CSV] --> B[初始化 IR 鉴权 (关闭)]

    %% 主循环
    B --> C{遍历每行数据}
    C -->|每行| D[process_row(row)]

    %% process_row 子流程
    D --> E[意图识别 get_intent]
    E -->|intents_llm| F[向量召回 vector_retrieval]
    F -->|api_desc| G[规划生成 planning_generation]
    G -->|tools_llm| H[参数提取 parameter_extraction]
    H --> I[返回 dict (row_result)]

    %% 写回结果
    I --> J[填充 DataFrame 列]
    J --> C

    %% 循环结束
    C --> K[恢复 IR 鉴权 (开启)]
    K --> L[保存结果至 Excel & 临时文件]

    %% 统计 & 评估
    L --> M[汇总统计 summarize_and_log]
    M --> N[调用 eva(output_path)]

    %% eva 子流程
    N --> O[IntentEvaluator → 计算意图准确率]
    N --> P[ToolsEvaluator  → 计算工具集合准确率/召回率]
    N --> Q[ParamsEvaluator → 计算参数准确率/召回率 + 差异报告]

    %% 结束
    O --> R[输出意图评估报告]
    P --> R
    Q --> R
    R --> S[结束]

    %% 样式
    classDef startend fill:#f9f,stroke:#333,stroke-width:2px;
    class A,S startend;
