<div align=center>
<img src="https://github.com/dgynfi/DYFProgressView/raw/master/images/DYFProgressView.png" width="70%">
</div>

[如果你觉得能帮助到你，请给一颗小星星。谢谢！(If you think it can help you, please give it a star. Thanks!)](https://github.com/dgynfi/DYFProgressView)

[![License MIT](https://img.shields.io/badge/license-MIT-green.svg?style=flat)](LICENSE)&nbsp;
[![CocoaPods](http://img.shields.io/cocoapods/v/DYFProgressView.svg?style=flat)](http://cocoapods.org/pods/DYFProgressView)&nbsp;
![CocoaPods](http://img.shields.io/cocoapods/p/DYFProgressView.svg?style=flat)&nbsp;

## DYFProgressView

&emsp; 超好用的进度条和网页进度条，操作简单好用。(Super useful progress bar and web page progress bar, the operation is simple and easy to use.)

## 技术交流群(群号:155353383) 

- 欢迎加入技术交流群，一起探讨技术问题。

<div align=left>
<img src="https://github.com/dgynfi/DYFProgressView/raw/master/images/qq155353383.jpg" width="20%" />
</div>

## 安装

- 支持通过 CocoaPods 安装。

```pod install
pod 'DYFProgressView', '~> 1.0.9'
```

或者

```
# Installs lastest version.
pod 'DYFProgressView'
```

## 效果图

<div align=left>
<img src="https://github.com/dgynfi/DYFProgressView/raw/master/images/ProgressViewPreview.gif" width="40%" />
</div>

## 使用说明

1. 实例化

```ObjC
// Lazy load
- (DYFWebProgressView *)progressView {
    if (!_progressView) {
        CGFloat w = self.navigationBar.bounds.size.width;
        CGFloat h = 3.f;
        CGFloat x = 0.f;
        CGFloat y = self.navigationBar.bounds.size.height - h;

        _progressView = [[DYFWebProgressView alloc] initWithFrame:CGRectMake(x, y, w, h)];
        _progressView.lineWidth = 3.f;
        _progressView.lineColor = [UIColor colorWithRed:RGB_V(10) 
                                                  green:RGB_V(115) 
                                                   blue:RGB_V(255) 
                                                  alpha:1];
    }
    return _progressView;
}

- (UINavigationBar *)navigationBar {
    return self.navigationController.navigationBar;
}
```

2. 添加到父视图

```
// 在开始加载进度前，调用它
- (void)loadView {
    [super loadView];
    [self addProgressView];
}

// 添加到父视图。
- (void)addProgressView {
    if (!_progressView) {
        [self.navigationBar addSubview:self.progressView];
    }
}
```

3. 开始加载进度

```
[self.progressView startLoading];
```

4. 结束加载进度

```
[self.progressView endLoading];
```

## 如何接入 WKWebView ?

1. Invoked when a main frame navigation starts.

```
- (void)webView:(WKWebView *)webView didStartProvisionalNavigation:(WKNavigation *)navigation {
    // didStartProvisionalNavigation.

    NSURL *aURL = [webView.URL copy];
    NSLog(@"%s url: %@", __FUNCTION__, aURL);

    [self.progressView startLoading];
}
```

2. Invoked when a server redirect is received for the main frame.

```
- (void)webView:(WKWebView *)webView didReceiveServerRedirectForProvisionalNavigation:(WKNavigation *)navigation {
    // didReceiveServerRedirectForProvisionalNavigation.
    NSLog(@"%s url: %@", __FUNCTION__, webView.URL);
}
```

3. Invoked when content starts arriving for the main frame.

```
- (void)webView:(WKWebView *)webView didCommitNavigation:(WKNavigation *)navigation {
    NSLog(@"%s", __FUNCTION__);
}
```

4. Invoked when a main frame navigation completes.

```
- (void)webView:(WKWebView *)webView didFinishNavigation:(WKNavigation *)navigation {
    NSLog(@"%s", __FUNCTION__);
    [self.progressView endLoading];
    [self setupNavigationItemTitle];
}
```

5. Invoked when an error occurs while starting to load data for the main frame.

```
- (void)webView:(WKWebView *)webView didFailProvisionalNavigation:(WKNavigation *)navigation withError:(NSError *)error {
    if (!error) { return; }

    NSString *errMessage = [NSString stringWithFormat:@"%zi, %@", error.code, error.localizedDescription];
    NSLog(@"%s [error]: %@", __FUNCTION__, errMessage);

    [self.progressView endLoading];
}
```

6. Invoked when an error occurs during a committed main frame navigation.

```
- (void)webView:(WKWebView *)webView didFailNavigation:(WKNavigation *)navigation withError:(NSError *)error {
    if (!error) { return; }

    NSString *errMessage = [NSString stringWithFormat:@"%zi, %@", error.code, error.localizedDescription];
    NSLog(@"%s [error]: %@", __FUNCTION__, errMessage);

    [self.progressView endLoading];
}
```

7. Decides whether to allow or cancel a navigation.

```
- (void)webView:(WKWebView *)webView decidePolicyForNavigationAction:(WKNavigationAction *)navigationAction decisionHandler:(void (^)(WKNavigationActionPolicy))decisionHandler {
    // decidePolicyForNavigationAction.
    [self setupNavigationItemTitle];

    NSURL *aURL = [navigationAction.request.URL copy];
    NSString *aUrl = aURL.absoluteString;
    NSLog(@"%s url: %@", __FUNCTION__, aUrl);

    if (![aUrl isEqualToString:@"about:blank"]) {}

    // Method NO.1: resolve the problem about '_blank'.
    //if (navigationAction.targetFrame == nil) {
        //NSLog(@"- [webView loadRequest:navigationAction.request]");
        //[webView loadRequest:navigationAction.request];
    //}

    decisionHandler(WKNavigationActionPolicyAllow);
}
```

8. Creates a new web view. Resolves the problem about '_blank'.

```
// Method NO.2: resolve the problem about '_blank'.
- (WKWebView *)webView:(WKWebView *)webView createWebViewWithConfiguration:(WKWebViewConfiguration *)configuration forNavigationAction:(WKNavigationAction *)navigationAction windowFeatures:(WKWindowFeatures *)windowFeatures {
    // createWebViewWithConfiguration.

    NSURL *aURL = [navigationAction.request.URL copy];
    NSString *aUrl = aURL.absoluteString;
    NSLog(@"%s url: %@", __FUNCTION__, aUrl);

    if (!navigationAction.targetFrame.isMainFrame) {
        NSLog(@"- [webView loadRequest:navigationAction.request]");
        [webView loadRequest:navigationAction.request];
    }

    return nil;
}
```

9. The navigation item’s title displayed in the navigation bar.

```
- (void)setupNavigationItemTitle {
    self.navigationItem.title = self.wk_webView.title;
}

```

## Sample Codes

- [Sample Codes Gateway](https://github.com/dgynfi/DYFProgressView/blob/master/Basic%20Files/DYFDisplayViewController.m)

