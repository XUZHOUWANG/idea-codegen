# idea-codegen
用groovy生产代码，修改idea tools中的脚本


# 应用

1. 将Generate POJOs groovy的脚本复制到idea->database,针对一张表右击，选择"tools"
2. 选择 Generate POJOs groovy，覆盖


# 碰到的问题

1. 自己怎么扩展？

idea的用户数据下有相应的源代码包，可以去看定义： /lib/database-openapi
> D:\Program Files\JetBrains\IntelliJ IDEA 2023.1.2\lib\src

2. 乱码怎么办？

groovy本质是JAVA,可以考虑他用流文件输出时，包装一个流（outputstreamwriter）, 并设置utf-8的格式
