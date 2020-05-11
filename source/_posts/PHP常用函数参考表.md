---
title: PHP常用函数参考表
date: 2019-05-05 22:26:40
tags:
    - PHP
categories:
    - PHP
---

各种常用的php函数，包括php字符串函数,php数组函数,php数学函数,mysql函数,文件目录函数,GD库函数,SESSION函数,Date/Time 函数等，涵盖了php基本操作所涉及的各类常用函数，并针对各个函数提供了简单的解释说明，方便查阅

<!-- more -->

# 字符串相关操作函数

## 去除空格或其他字符

| 方法 | 说明 |
| ---- | ---- |
| trim | 删除字符串两端空格或其他预定义字符 |
| rtrim | 删除字符串右边空格或其他预定义字符 |
| chop | rtrim() 的别名 chop() 与 Perl 的 chop() 函数有所不同，它会删除字符串的最后一个字符。 |
| ltrim | 删除字符串左边空格或其他预定义字符 |

## 字符串生成与转换

| 方法 | 说明 |
| ---- | ---- |
| str_pad | 使用另一个字符串填充字符串为指定长度 |
| str_replace | 子字符串替换 |
| str_split | 将字符串转换为数组 |
| strrev | 反转字符串 |
| wordwrap | 打断字符串为指定数量的字串 |
| str_shuffle | 随机打乱一个字符串 |
| parse_str | 将字符串解析成变量 |
| number_format | 通过千位分组来格式化字符串 |

## 字符串大小写转换

| 方法 | 说明 |
| ---- | ---- |
| strtolower | 将字符串转化为小写 |
| strtoupper | 将字符串转化为大写 |
| ucfirst | 将字符串的首字母转换为大写 |
| ucwords | 将字符串中每个单词的首字母转换为大写 |

## html标签关联

| 方法 | 说明 |
| ---- | ---- |
| htmlentities | 将字符转换为HTML实体 |
| htmlspecialchars | 预定义字符串转HTML编码 |
| nl2br | 在字符串所有新行之前插入 HTML 换行标记 `\n`转换为`<br>`标签 | 
| strip_tags | 从字符串中去除 HTML 和 PHP 标记 |
| addcslashes | 以 C 语言风格使用反斜线转义字符串中的字符 |
| stripcslashes | 反引用一个使用 addcslashes() 转义的字符串 |
| addslashes | 使用反斜线引用字符串 |
| stripslashes | 删除由addslashes添加的转义字符 |
| quotemeta | 字符串中某些预定义字符串前加反斜杠 |
| chr | 从指定的 ASCII 值返回字符 |
| ord | 返回字符串第一个字符的 ASCII 值 |

## 字符串比较

| 方法 | 说明 |
| ---- | ---- |
| strcasecmp | 不区分大小写比较两个字符串 |
| strcmp | 区分大小写比较两个字符串 |
| strncmp | 比较字符串前N个字符，区分大小写 |
| strncasecmp | 比较字符串前N个字符，不区分大小写 |
| strnatmp | 自然顺序法比较字符串长度，区分大小写 |
| strnatcasecmp | 自然顺序法比较字符串长度，不区分大小写 |

## 字符串查找替换

| 方法 | 说明 |
| ---- | ---- |
| str_replace | 字符串替换操作，区分大小写 |
| str_ireplace | 字符串替换操作，不区分大小写 |
| substr_count | 统计一个字符串，在另一个字符串中出现的次数 |
| substr_replace | 替换字符串中某串为另一个字符串 |
| similar_text | 返回两字符串相同字符的数量 |
| strrchr | 返回一个字符串在另一个字符串中最后一次出现位置开始到末尾的字符串 |
| strstr | 返回一个字符串在另一个字符串中开始位置到结束位置的字符串 |
| strchr | strstr的别名，返回一个字符串在另一个中首次出现位置开始到末尾的字符串 |
| stristr | 返回一个字符串在另一个字符串中开始位置到结束位置的字符串，不区分大小写 |
| strtr | 转换字符串中某些字符 |
| strpos | 寻找字符中某些字符最先出现的位置 |
| stripos | 寻找字符中某些字符最先出现的位置，不区分大小写 |
| strrpos | 寻找字符中某些字符最后出现的位置 |
| strripos | 寻找字符中某些字符最后出现的位置，不区分大小写 |
| strspn | 返回字符串中首次符合mask的字符串长度 |
| strcspn | 返回字符串中不符合mask的字符串的长度 |

## 字符串统计

| 方法 | 说明 |
| ---- | ---- |
| str_word_count | 统计字符串含有的单词数 |
| strlen | 统计字符串长度 |
| count_chars | 统计字符串中所有字母出现的次数（0..255） |

## 字符串编码

| 方法 | 说明 |
| ---- | ---- |
| md5 | 计算字符串的 MD5 散列值 |
| hash | 生成一个哈希码 |

# 数组相关函数

## 创建数组

| 方法 | 说明 |
| ---- | ---- |
| array | 生成一个数组 |
| array_combine | 生成一个数组，用一个数组的值作为键名，另一个数组的值作为值 |
| range | 创建并返回一个包含指定范围的元素的数组 |
| compact | 创建一个由参数所带变量组成的数组 |
| array_fill | 用给定的值填充生成数组 |

## 数组合并和拆分

| 方法 | 说明 |
| ---- | ---- |
| array_chunk | 把一个数组分割为新的数组块 |
| array_merge | 把两个或多个数组合并成一个数组 |
| array_slice | 在数组中根据条件取出一段值，并返回。 |

## 数组比较

| 方法 | 说明 |
| ---- | ---- |
| array_diff | 返回两个数组的差集数组 |
| array_intersect | 返回两个或多个数组的交集数组 |

## 数组查找替换

| 方法 | 说明 |
| ---- | ---- |
| array_search | 在数组中查找一个键值 |
| array_splice | 把数组一部分删除把其他值替代 |
| array_sum | 返回数组中所有值的总和 |
| in_array | 在数组中搜索指定的值，区分大小写 |
| array_key_exists | 判断某个数组中是否存在指定的key |

## 数组指针操作

| 方法 | 说明 |
| ---- | ---- |
| key | 返回数组内部指针当前指向的键名 |
| current | 返回数组中的当前元素 |
| next | 把指向当前元素的指针移动到下一个元素位置，并返回当前元素的值 |
| prev | 把指向当前元素的指针移动到上一个元素位置，并返回当前元素的值 |
| end | 将数组内部指针指向最后一个元素，并返回该元素的值（如果成功） |
| reset | 把数组内部指针指向第一个元素，并返回该元素的值 |
| list | 用数组中的元素为一组变量赋值 |
| array_shift | 删除数组中的第一个元素，并返回被删除元素的值 |
| array_unshif | 在数组开头插入一个或者多个元素 |
| array_push | 向数组最后压入一个或多个元素 |
| array_pop | 删除数组最后一个元素 |

## 数组键值操作

| 方法 | 说明 |
| ---- | ---- |
| shuffle | 将数组打乱，保留键名 |
| count | 计算数组中单元个数或者对象属性个数 |
| array_flip | 返回一个键值翻转后的数组 |
| array_keys | 返回数组所有键，组成一个数组 |
| array_values | 返回数组所有值，组成一个数组 |
| array_reverse | 返回一个元素顺序相反的数组 |
| array_count_values | 统计数组中所有值出现的次数 |
| array_rand | 从数组中随机抽取一个或者多个元素，注意是键名 |
| each | 返回数组中当前的键／值对并将数组指针向前移动一步 |
| array_unique | 移除数组中重复的值 |

## 数组排序

| 方法 | 说明 |
| ---- | ---- |
| sort | 对数组排序 |
| rsort | 对数组逆向排序 |
| asort | 对数组进行排序并保持索引关系 |
| arsort | 对数组进行逆向排序并保持索引关系 |
| ksort | 对数组按照键名排序 | 
| krsort | 对数组按照键名逆向排序 |
| natsort | 用“自然排序”算法对数组排序 |
| natcasesort | 用“自然排序”算法对数组进行不区分大小写字母的排序 |

# 数学相关函数

| 方法 | 说明 |
| ---- | ---- |
| abs | 求绝对值 |
| ceil | 进一法取整 |
| floor | 舍去法取整 |
| fmod | 返回除法的浮点数余数 |
| pow | 返回数的N次方 |
| round | 浮点数四舍五入法 |
| sqrt | 求平方根 |
| max | 求最大值 |
| min | 求最小值 |
| mt_rand | 更好的随机数 |
| rand | 随机数 |
| pi | 获得圆周率 |
| octdec |  八进制转换为十进制 |

# mysql相关函数

| 方法 | 说明 |
| ---- | ---- |
| mysql_affected_rows | 取得前一次 MySQL 操作所影响的记录行数 |
| mysql_client_encoding | 返回字符集的名称 |
| mysql_close | 关闭 MySQL 连接 |
| mysql_connect | 打开一个到 MySQL 服务器的连接 |
| mysql_create_db | 新建一个 MySQL 数据库 |
| mysql_data_seek | 移动内部结果的指针 |
| mysql_db_name | 取得结果数据 |
| mysql_db_query | 发送一条 MySQL 查询 |
| mysql_drop_db | 丢弃（删除）一个 MySQL 数据库 |
| mysql_errno | 返回上一个 MySQL 操作中的错误信息的数字编码 |
| mysql_error | 返回上一个 MySQL 操作产生的文本错误信息 |
| mysql_escape_string | 转义一个字符串用于 mysql_query |
| mysql_fetch_array | 从结果集中取得一行作为关联数组，或数字数组，或二者兼有 |
| mysql_fetch_assoc | 从结果集中取得一行作为关联数组 |
| mysql_fetch_field | 从结果集中取得列信息并作为对象返回 |
| mysql_fetch_lengths | 取得结果集中每个输出的长度 |
| mysql_fetch_object | 从结果集中取得一行作为对象 |
| mysql_fetch_object | 从结果集中取得一行作为枚举数组 |
| mysql_field_flags | 从结果中取得和指定字段关联的标志 |
| mysql_field_len | 返回指定字段的长度 |
| mysql_field_name | 取得结果中指定字段的字段名 |
| mysql_field_seek | 将结果集中的指针设定为制定的字段偏移量 |
| mysql_field_table | 取得指定字段所在的表名 |
| mysql_field_type | 取得结果集中指定字段的类型 |
| mysql_free_result | 释放结果内存 |
| mysql_get_client_info | 取得 MySQL 客户端信息 |
| mysql_get_host_info | 取得 MySQL 主机信息 |
| mysql_get_proto_info | 取得 MySQL 协议信息 |
| mysql_get_server_info | 取得 MySQL 服务器信息 |
| mysql_info | 取得最近一条查询的信息 |
| mysql_insert_id | 取得上一步 INSERT 操作产生的 ID |
| mysql_list_dbs | 列出 MySQL 服务器中所有的数据库 |
| mysql_list_fields | 列出 MySQL 结果中的字段 |
| mysql_list_processes | 列出 MySQL 进程 |
| mysql_list_tables | 列出 MySQL 数据库中的表 |
| mysql_num_fields | 取得结果集中字段的数目 |
| mysql_num_rows | 取得结果集中行的数目 |
| mysql_pconnect | 打开一个到 MySQL 服务器的持久连接 |
| mysql_ping | Ping 一个服务器连接，如果没有连接则重新连接 |
| mysql_query | 发送一条 MySQL 查询 |
| mysql_real_escape_string | 转义 SQL 语句中使用的字符串中的特殊字符，并考虑到连接的当前字符集 |
| mysql_result | 取得结果数据 |
| mysql_select_db | 选择 MySQL 数据库 |
| mysql_set_charset | 设置客户端字符集 |
| mysql_stat | 取得当前系统状态 |
| mysql_tablename | 取得表名 |
| mysql_thread_id | 返回当前线程的 ID |
| mysql_unbuffered_query | 向 MySQL 发送一条 SQL 查询，并不获取和缓存结果的行 |

# 文件目录处理相关函数

| 方法 | 说明 |
| ---- | ---- |
| basename | 返回路径中的文件名部分 |
| chgrp | 改变文件所属的组 |
| chmod | 改变文件模式 |
| chown | 改变文件的所有者 |
| clearstatcache | 清除文件状态缓存 |
| copy | 拷贝文件 |
| delete | 参见 unlink 或 unset |
| dirname | 返回路径中的目录部分 |
| disk_free_space | 返回目录中的可用空间 |
| disk_total_space | 返回一个目录的磁盘总大小 |
| diskfreespace | disk_free_space 的别名 |
| fclose | 关闭一个已打开的文件指针 |
| feof | 测试文件指针是否到了文件结束的位置 |
| fflush | 将缓冲内容输出到文件 |
| fgetc | 从文件指针中读取字符 |
| fgetcsv | 从文件指针中读入一行并解析 CSV 字段 |
| fgets | 从文件指针中读取一行 |
| fgetss | 从文件指针中读取一行并过滤掉 HTML 标记 |
| file_exists | 检查文件或目录是否存在 |
| file_get_contents | 将整个文件读入一个字符串 |
| file_put_contents | 将一个字符串写入文件 |
| file | 把整个文件读入一个数组中 |
| fileatime | 取得文件的上次访问时间 |
| filectime | 取得文件的 inode 修改时间 |
| filegroup | 取得文件的组 |
| fileinode | 取得文件的 inode |
| filemtime | 取得文件修改时间 |
| fileowner | 取得文件的所有者 |
| fileperms | 取得文件的权限 |
| filesize | 取得文件大小 |
| filetype | 取得文件类型 |
| flock | 轻便的咨询文件锁定 |
| fnmatch | 用模式匹配文件名 |
| fopen | 打开文件或者 URL |
| fpassthru | 输出文件指针处的所有剩余数据 |
| fputcsv | 将行格式化为 CSV 并写入文件指针 |
| fputs |   fwrite 的别名 |
| fread | 读取文件（可安全用于二进制文件) |
| fscanf | 从文件中格式化输入 |
| fseek | 在文件指针中定位 |
| fstat | 通过已打开的文件指针取得文件信息 |
| ftell |   返回文件指针读/写的位置 |
| ftruncate | 将文件截断到给定的长度 |
| fwrite | 写入文件（可安全用于二进制文件） |
| glob | 寻找与模式匹配的文件路径 |
| is_dir | 判断给定文件名是否是一个目录 |
| is_executable | 判断给定文件名是否可执行 |
| is_file | 判断给定文件名是否为一个正常的文件 |
| is_link | 判断给定文件名是否为一个符号连接 |
| is_readable | 判断给定文件名是否可读 |
| is_uploaded_file | 判断文件是否是通过 HTTP POST 上传的 |
| is_writable | 判断给定的文件名是否可写 |
| is_writeable | is_writable 的别名 |
| lchgrp | Changes group ownership of symlink |
| lchown | Changes user ownership of symlink |
| link |  建立一个硬连接 |
| linkinfo | 获取一个连接的信息 |
| lstat | 给出一个文件或符号连接的信息 |
| mkdir | 新建目录 |
| move_uploaded_file | 将上传的文件移动到新位置 |
| parse_ini_file |  解析一个配置文件 |
| parse_ini_string | Parse a configuration string |
| pathinfo | 返回文件路径的信息 |
| pclose | 关闭进程文件指针 |
| popen | 打开进程文件指针 |
| readfile | 输出一个文件 |
| readlink | 返回符号连接指向的目标 |
| realpath_cache_get | Get realpath cache entries |
| realpath_cache_size | Get realpath cache size |
| realpath | 返回规范化的绝对路径名 |
| rename | 重命名一个文件或目录 |
| rewind | 倒回文件指针的位置 |
| rmdir | 删除目录 |
| set_file_buffer | stream_set_write_buffer 的别名 |
| stat | 给出文件的信息 |
| symlink | 建立符号连接 |
| tempnam | 建立一个具有唯一文件名的文件 |
| tmpfile | 建立一个临时文件 |
| touch | 设定文件的访问和修改时间 |
| umask | 改变当前的 umask |
| unlink | 删除文件 |

# GD/Image函数

| 方法 | 说明 |
| ---- | ---- |
| gd_info | 取得当前安装的 GD 库的信息 |
| getimagesize  |  取得图像大小 |
| getimagesizefromstring | Get the size of an image from a string |
| image_type_to_extension | 取得图像类型的文件后缀 |
| image_type_to_mime_type | 取得 getimagesize，exif_read_data，exif_thumbnail，exif_imagetype 所返回的图像类型的 MIME 类型 |
| image2wbmp | 以 WBMP 格式将图像输出到浏览器或文件 |
| imagealphablending | 设定图像的混色模式 |
| imageantialias | 是否使用抗锯齿（antialias）功能 |
| imagearc   | 画椭圆弧 |
| imagechar  | 水平地画一个字符 |
| imagecharup | 垂直地画一个字符 |
| imagecolorallocate | 为一幅图像分配颜色 |
| imagecolorallocatealpha | 为一幅图像分配颜色 + alpha |
| imagecolorat  |  取得某像素的颜色索引值 |
| imagecolorclosest |  取得与指定的颜色最接近的颜色的索引值 |
| imagecolorclosestalpha | 取得与指定的颜色加透明度最接近的颜色 |
| imagecolorclosesthwb  |  取得与给定颜色最接近的色度的黑白色的索引 |
| imagecolordeallocate  |  取消图像颜色的分配 |
| imagecolorexact | 取得指定颜色的索引值 |
| imagecolorexactalpha  |  取得指定的颜色加透明度的索引值 |
| imagecolormatch | 使一个图像中调色板版本的颜色与真彩色版本更能匹配 |
| imagecolorresolve  | 取得指定颜色的索引值或有可能得到的最接近的替代值 |
| imagecolorresolvealpha | 取得指定颜色 + alpha 的索引值或有可能得到的最接近的替代值 |
| imagecolorset  | 给指定调色板索引设定颜色 |
| imagecolorsforindex | 取得某索引的颜色 |
| imagecolorstotal  |  取得一幅图像的调色板中颜色的数目 |
| imagecolortransparent  | 将某个颜色定义为透明色 |
| imageconvolution   | 用系数 div 和 offset 申请一个 3x3 的卷积矩阵 |
| imagecopy  | 拷贝图像的一部分 |
| imagecopymerge | 拷贝并合并图像的一部分 |
| imagecopymergegray | 用灰度拷贝并合并图像的一部分 |
| imagecopyresampled | 重采样拷贝部分图像并调整大小 |
| imagecopyresized   | 拷贝部分图像并调整大小 |
| imagecreate | 新建一个基于调色板的图像 |
| imagecreatefromgd2 | 从 GD2 文件或 URL 新建一图像 |
| imagecreatefromgd2part | 从给定的 GD2 文件或 URL 中的部分新建一图像 |
| imagecreatefromgd  | 从 GD 文件或 URL 新建一图像 |
| imagecreatefromgif | 由文件或URL创建一个新图象 |
| imagecreatefromjpeg | 由文件或URL创建一个新图象 |
| imagecreatefrompng | 由文件或URL创建一个新图象 |
| imagecreatefromstring |  从字符串中的图像流新建一图像 |
| imagecreatefromwbmp | 由文件或URL创建一个新图象 |
| imagecreatefromxbm | 由文件或URL创建一个新图象 |
| imagecreatefromxpm | 由文件或URL创建一个新图象 |
| imagecreatetruecolor  |  新建一个真彩色图像 |
| imagedashedline | 画一虚线 |
| imagedestroy  |  销毁一图像 |
| imageellipse  |  画一个椭圆 |
| imagefill  | 区域填充 |
| imagefilledarc | 画一椭圆弧且填充 |
| imagefilledellipse | 画一椭圆并填充 |
| imagefilledpolygon | 画一多边形并填充 |
| imagefilledrectangle  |  画一矩形并填充 |
| imagefilltoborder  | 区域填充到指定颜色的边界为止 |
| imagefilter | 对图像使用过滤器 |
| imagefontheight  | 取得字体高度 |
| imagefontwidth | 取得字体宽度 |
| imageftbbox | 给出一个使用 FreeType 2 字体的文本框 |
| imagefttext | 使用 FreeType 2 字体将文本写入图像 |
| imagegammacorrect |  对 GD 图像应用 gamma 修正 |
| imagegd2   | 将 GD2 图像输出到浏览器或文件 |
| imagegd | 将 GD 图像输出到浏览器或文件 |
| imagegif  |  输出图象到浏览器或文件 |
| imagegrabscreen | Captures the whole screen |
| imagegrabwindow | Captures a window |
| imageinterlace | 激活或禁止隔行扫描 |
| imageistruecolor   | 检查图像是否为真彩色图像 |
| imagejpeg  | 输出图象到浏览器或文件 |
| imagelayereffect  |  设定 alpha 混色标志以使用绑定的 libgd 分层效果 |
| imageline  | 画一条线段 |
| imageloadfont |  载入一新字体 |
| imagepalettecopy  |  将调色板从一幅图像拷贝到另一幅 |
| imagepng |   以 PNG 格式将图像输出到浏览器或文件 |
| imagepolygon  |  画一个多边形 |
| imagepsbbox | 给出一个使用 PostScript Type1 字体的文本方框 |
| imagepsencodefont  | 改变字体中的字符编码矢量 |
| imagepsextendfont |  扩充或精简字体 |
| imagepsfreefont | 释放一个 PostScript Type 1 字体所占用的内存 |
| imagepsloadfont  | 从文件中加载一个 PostScript Type 1 字体 |
| imagepsslantfont  |  倾斜某字体 |
| imagepstext | 用 PostScript Type1 字体把文本字符串画在图像上 |
| imagerectangle | 画一个矩形 |
| imagerotate  | 用给定角度旋转图像 |
| imagesavealpha | 设置标记以在保存 PNG 图像时保存完整的 alpha 通道信息（与单一透明色相反） |
| imagesetbrush  | 设定画线用的画笔图像 |
| imagesetpixel  | 画一个单一像素 |
| imagesetstyle  | 设定画线的风格 |
| imagesetthickness |  设定画线的宽度 |
| imagesettile  |  设定用于填充的贴图 |
| imagestring  | 水平地画一行字符串 |
| imagestringup |  垂直地画一行字符串 |
| imagesx | 取得图像宽度 |
| imagesy | 取得图像高度 |
| imagetruecolortopalette | 将真彩色图像转换为调色板图像 |
| imagettfbbox  |  取得使用 TrueType 字体的文本的范围 |
| imagettftext |   用 TrueType 字体向图像写入文本 |
| imagetypes | 返回当前 PHP 版本所支持的图像类型 |
| imagewbmp  | 以 WBMP 格式将图像输出到浏览器或文件 |
| imagexbm  |  将 XBM 图像输出到浏览器或文件 |
| iptcembed  | 将二进制 IPTC 数据嵌入到一幅 JPEG 图像中 |
| iptcparse |  将二进制 IPTC http://www.iptc.org/ 块解析为单个标记 |
| jpeg2wbmp |  将 JPEG 图像文件转换为 WBMP 图像文件 |
| png2wbmp  |  将 PNG 图像文件转换为 WBMP 图像文件 |

# session函数

| 方法 | 说明 |
| ---- | ---- |
| session_cache_expire |    返回当前缓存到期 |
| session_cache_limiter | get和set the current缓存限制/金 |
| session_commit | session_write_close 的别名 |
| session_decode |  将会话数据从一个会话编码的字符串 |
| session_destroy | 销毁已注册到会话的所有数据 |
| session_encode | 将当前会话数据编码为一个字符串 |
| session_get_cookie_params | 获得会话饼干参数 |
| session_id | 获得和/或设置当前会话标识 |
| session_is_registered | 检查变量是否在会话中已经注册 |
| session_module_name | 获得和/或设置当前会话模块 |
| session_name | 获得和/或设置当前会话名称 |
| session_regenerate_id | 更新新生成的会话标识 |
| session_register_shutdown | 会话关机功能 |
| session_register | 注册一个或多个全局变量与当前会话 |
| session_save_path | 获取和/或设置当前会话保存路径 |
| session_set_cookie_params |   设置会话饼干参数 |
| session_set_save_handler | 设置用户级会话存储功能 |
| session_start | 开始新的或恢复现有的会话 |
| session_status | 返回当前会话状态 |
| session_unregister | 注销当前会话的一个全局变量 |
| session_unset | 自由所有会话变量 |
| session_write_close | 写会话数据和结束会话 |

# cookie函数

| 方法 | 说明 |
| ---- | ---- |
| setcookie | 设置cookie |
| setrawcookie | 发送没有URL编码的cookie |

# Date/Time 函数

| 方法 | 说明 |
| ---- | ---- |
| checkdate | 验证一个格里高里日期 |
| date_add | 别名 DateTime::add |
| date_create_from_format | 别名 DateTime::createFromFormat |
| date_create | 别名 DateTime::__construct |
| date_date_set | 别名 DateTime::setDate |
| date_default_timezone_get  | 取得一个脚本中所有日期时间函数所使用的默认时区 |
| date_default_timezone_set | 设定用于一个脚本中所有日期时间函数的默认时区 |
| date_diff | 别名 DateTime::diff |
| date_format | 别名 DateTime::format |
| date_get_last_errors | 别名 DateTime::getLastErrors |
| date_interval_create_from_date_string | 别名 DateInterval::createFromDateString |
| date_interval_format | 别名 DateInterval::format |
| date_isodate_set | 别名 DateTime::setISODate |
| date_modify | 别名 DateTime::modify |
| date_offset_get | 别名 DateTime::getOffset |
| date_parse_from_format | Get info about given date formatted according to the specified format |
| date_parse | Returns associative array with detailed info about given date |
| date_sub | 别名 DateTime::sub |
| date_sun_info | Returns an array with information about sunset/sunrise and twilight begin/end |
| date_sunrise  | 返回给定的日期与地点的日出时间 |
| date_sunset | 返回给定的日期与地点的日落时间 |
| date_time_set  | 别名 DateTime::setTime |
| date_timestamp_get | 别名 DateTime::getTimestamp |
| date_timestamp_set | 别名 DateTime::setTimestamp |
| date_timezone_get | 别名 DateTime::getTimezone |
| date_timezone_set | 别名 DateTime::setTimezone |
| date | 格式化一个本地时间／日期 |
| getdate | 取得日期／时间信息 |
| gettimeofday | 取得当前时间 |
| gmdate | 格式化一个 GMT/UTC 日期／时间 |
| gmmktime | 取得 GMT 日期的 UNIX 时间戳 |
| gmstrftime | 根据区域设置格式化 GMT/UTC 时间／日期 |
| idate | 将本地时间日期格式化为整数 |
| localtime | 取得本地时间 |
| microtime | 返回当前 Unix 时间戳和微秒数 |
| mktime | 取得一个日期的 Unix 时间戳 |
| strftime | 根据区域设置格式化本地时间／日期 |
| strptime | 解析由 strftime 生成的日期／时间 |
| strtotime | 将任何英文文本的日期时间描述解析为 Unix 时间戳 |
| time | 返回当前的 Unix 时间戳 |
| timezone_abbreviations_list | 别名 DateTimeZone::listAbbreviations |
| timezone_identifiers_list | 别名 DateTimeZone::listIdentifiers |
| timezone_location_get | 别名 DateTimeZone::getLocation |
| timezone_name_from_abbr | Returns the timezone name from abbreviation |
| timezone_name_get | 别名 DateTimeZone::getName |
| timezone_offset_get | 别名 DateTimeZone::getOffset |
| timezone_open | 别名 DateTimeZone::__construct |
| timezone_transitions_get  | 别名 DateTimeZone::getTransitions |
| timezone_version_get  | Gets the version of the timezonedb |






















