---
title: "Tính chiều cao dự kiến của UILabel áp dụng cho trường hợp Show More."
date: 2022-01-21T23:21:39+09:00
slug: Tinh-chieu-cao-du-kien-cua-UILabel-ap-dung-cho-truong-hop-Show-More
type: posts
draft: false
categories:
  - default
tags:
  - ios
  - iphone
  - objectivec
---

Xin chào mọi người. Mình xin chia sẽ một **UILabel Helper** nhỏ dùng trong truờng hợp **cần tính chiều cao của UILabel để xác định "Show More" button có nên được hiển thị hay không**.

### Đây là helper của mình:
*Có một hạn chế ở helper này là bạn đã xác định được chiều rộng của UILabel. Mình sẽ khắc phục và update sau.*

```
+ (float)expectedHeight:(NSString *)text width:(float)width font:(UIFont *)font;
{
  NSDictionary *attributesDictionary = [NSDictionary dictionaryWithObjectsAndKeys:
                                        font, NSFontAttributeName,
                                        nil];

  CGSize maximumLabelSize = CGSizeMake(width, 9999);

  CGRect expectedLabelRect = [text boundingRectWithSize:maximumLabelSize
                                                options:(NSStringDrawingUsesLineFragmentOrigin | NSStringDrawingUsesFontLeading)
                                             attributes:attributesDictionary
                                                context:nil];
  CGSize *expectedLabelSize = &expectedLabelRect.size;

  return expectedLabelSize->height;
}
```

Hy vọng bài viết có ích. Cảm ơn mọi người!

