---
title: iOS动态更改icon图标
date: 2018-01-05 11:47:40
categories: 笔记
tags: 
toc:
---

1.将所有@2x，@3x图标放到项目中

2.配置info.plist
<!--more-->
```
<key>CFBundleIcons</key>
<dict>
    <key>CFBundlePrimaryIcon</key>
    <dict>
        <key>CFBundleIconFiles</key>
        <array>
            <string>appIcon1</string>
        </array>
    </dict>
    <key>CFBundleAlternateIcons</key>
    <dict>
        <key>appIcon1</key>
        <dict>
            <key>CFBundleIconFiles</key>
            <array>
                <string>appIcon1</string>
            </array>
            <key>UIPrerenderedIcon</key>
            <string>NO</string>
        </dict>
        
        <key>appIcon2</key>
        <dict>
            <key>CFBundleIconFiles</key>
            <array>
                <string>appIcon2</string>
            </array>
            <key>UIPrerenderedIcon</key>
        <string>NO</string>
        </dict>
        
        <key>appIcon3</key>
        <dict>
            <key>CFBundleIconFiles</key>
            <array>
                <string>appIcon3</string>
            </array>
            <key>UIPrerenderedIcon</key>
            <string>NO</string>
        </dict>
    </dict>
</dict>
```

3.更换图标(仅限于iOS版本10.3以上)
```
- (void)changeIcon{

    if ([UIApplication sharedApplication].supportsAlternateIcons) {
        NSLog(@"you can change this app's icon");
    }else{
        NSLog(@"you can not change this app's icon");
        return;
    }

    NSString *iconName = [[UIApplication sharedApplication] alternateIconName];
    NSLog(@"%@",iconName);
    if (iconName) {
        // change to primary icon
        [[UIApplication sharedApplication] setAlternateIconName:@"appIcon1" completionHandler:^(NSError * _Nullable error) {
            if (error) {
            NSLog(@"set icon error: %@",error);
            }
            NSLog(@"The alternate icon's name is %@",iconName);
        }];
    }else{
        // change to alterante icon
        [[UIApplication sharedApplication] setAlternateIconName:@"appIcon2" completionHandler:^(NSError * _Nullable error) {
            if (error) {
            NSLog(@"set icon error: %@",error);
            }
            NSLog(@"The alternate icon's name is %@",iconName);
        }];
    }
}
```


