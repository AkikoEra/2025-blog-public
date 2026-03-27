在Arch Linux 上玩steam原生星露谷物语，在联机界面一只显示正在链接到在线服务......，因此我觉得可能是因为Arch linux原生运行星露谷物语时的一个bug，我在一篇文章中正好找到了原因，https://yamr.cc/posts/err-smapi-galaxy-glibc-2-41/
问题是由于 glibc 在 2.41 版本引入的兼容性变化，导致 GOG Galaxy API 库（libGalaxy64.so 和 libGalaxyCSharpGlue.so）加载失败。具体原因是新版本对 GNU_STACK 段进行了更严格的检查。
解决方法#
1.安装 patchelf 或者其他工具：
sudo pacman -S patchelf
2.找到 libGalaxy64.so 和 libGalaxyCSharpGlue.so 文件的位置。在星露谷物语安装目录下，通常位于 ~/.steam/steam/steamapps/common/Stardew\ Valley 目录下。
3.使用 patchelf 去除 execstack 段：
patchelf --clear-execstack libGalaxy64.so
patchelf --clear-execstack libGalaxyCSharpGlue.so
搞定！ 通常情况下，执行完以上步骤，Error initializing the Galaxy api 就应该消失了，如果没有，那你遇到的大概不是这个问题。恢复也很简单，删掉这两个库，使用Steam检查游戏文件完整性即可。