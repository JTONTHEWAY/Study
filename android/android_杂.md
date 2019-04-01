# android学习小笔记
## 快速定位当前页面的三种方法(activity/fragment)

方法一、通过adb命令打印当前页面：

Android 如何快速定位当前页面是哪个Activity or Fragment

(1)查看当前Activity ：adb shell "dumpsys window w | grep name="

(2)查看当前栈顶的Activity ：adb shell dumpsys activity | grep "mFocusedActivity"

(3)查看当前栈顶的Activity的Fragment ：adb shell dumpsys activity your.package.name
或者：
adb shell dumpsys activity top

查看帮助：adb shell dumpsys activity -h

方法二：利用logcat，查看生命周期

方法三： as中通过Layout Inspector查看当前页面的调用栈
