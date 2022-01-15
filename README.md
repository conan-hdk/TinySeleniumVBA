# TinySeleniumVBA

A tiny Selenium wrapper written in pure VBA.

[🇯🇵日本語のREADMEはこちら](https://github.com/uezo/TinySeleniumVBA/blob/main/README.ja.md)

[🇧🇷Versão em Português](https://github.com/tdmsoares/TinySeleniumVBA/blob/ReadmeInPortuguese/README.pt.md)


# ✨ Features

- No installation: Everyone even who doesn't have permissions to install can automate browser operations.
- Useful helper Methods: FindElement(s)By*, Get/Set value to form, click and more.
- Open spec: Basically this wrapper is just a HTTP client of WebDriver server. Learning this wrapper equals to learning WebDriver.
https://www.w3.org/TR/webdriver/


# 📦 Setup

1. Set reference to `Microsoft Scripting Runtime`

1. Add `WebDriver.cls`, `WebElement.cls`, `Capabilities.cls` and `JsonConverter.bas` to your VBA Project
    - Latest (v0.1.2): https://github.com/uezo/TinySeleniumVBA/releases/tag/v0.1.2

1. Download WebDriver (driver and browser should be the same version)
    - Edge: https://developer.microsoft.com/ja-jp/microsoft-edge/tools/webdriver/
    - Chrome: https://chromedriver.chromium.org/downloads

# 🪄 Usage

```vb
Public Sub main()
    ' Start WebDriver (Edge)
    Dim Driver As New WebDriver
    Driver.Edge "path\to\msedgedriver.exe"

    ' Open browser
    Driver.OpenBrowser
    
    ' Navigate to Google
    Driver.Navigate "https://www.google.co.jp/?q=selenium"

    ' Get search textbox
    Dim searchInput
    Set searchInput = Driver.FindElement(By.Name, "q")
    
    ' Get value from textbox
    Debug.Print searchInput.GetValue
    
    ' Set value to textbox
    searchInput.SetValue "yomoda soba"
    
    ' Click search button
    Driver.FindElement(By.Name, "btnK").Click
    
    ' Refresh - you can use Execute with driver command even if the method is not provided
    Driver.Execute Driver.CMD_REFRESH
End Sub
```

# 🐙 BrowserOptions

Use `Capabilities` to configure browser options. This is an example to launch browser as headless (invisible) mode.

```vb
' Start web driver
Dim Driver As New WebDriver
Driver.Chrome "C:\path\to\chromedriver.exe"

' Configure Capabilities
Dim cap As Capabilities
Set cap = Driver.CreateCapabilities()
cap.AddArgument "--headless"
' Use SetArguments if you want to add multiple arguments
' cap.SetArguments "--headless --xxx -xxx"

' Show Capabilities as JSON for debugging
Debug.Print cap.ToJson()

' Open browser
Driver.OpenBrowser cap
```

See also the sites below to understand the spec of `Capabilities` for each browser.
- Chrome: https://chromedriver.chromium.org/capabilities
- Edge: https://docs.microsoft.com/en-us/microsoft-edge/webdriver-chromium/capabilities-edge-options


# ⚡️ Execute JavaScript

Use `ExecuteScript()` to execute JavaScript on the browser.

```vb
' Start web driver
Dim Driver As New WebDriver
Driver.Chrome "C:\path\to\chromedriver.exe"

' Open browser
Driver.OpenBrowser

' Navigate to Google
Driver.Navigate "https://www.google.co.jp/?q=liella"

' Show alert
Driver.ExecuteScript "alert('Hello TinySeleniumVBA')"

' === Use breakpoint to CLOSE ALERT before continue ===

' Pass argument
Driver.ExecuteScript "alert('Hello ' + arguments[0] + ' as argument')", Array("TinySeleniumVBA")

' === Use breakpoint to CLOSE ALERT before continue ===

' Pass element as argument
Dim searchInput
Set searchInput = Driver.FindElement(By.Name, "q")
Driver.ExecuteScript "alert('Hello ' + arguments[0].value + ' ' + arguments[1])", Array(searchInput, "TinySeleniumVBA")

' === CLOSE ALERT and continue ===

' Get return value from script
Dim retStr As String
retStr = Driver.ExecuteScript("return 'Value from script'")
Debug.Print retStr

' Get WebElement as return value from script
Dim firstDiv As WebElement
Set firstDiv = Driver.ExecuteScript("return document.getElementsByTagName('div')[0]")
Debug.Print firstDiv.GetText()

' Get complex structure as return value from script
Dim retArray
retArray = Driver.ExecuteScript("return [['a', '1'], {'key1': 'val1', 'key2': document.getElementsByTagName('div'), 'key3': 'val3'}]")

Debug.Print retArray(0)(0)  ' a
Debug.Print retArray(0)(1)  ' 1

Debug.Print retArray(1)("key1") ' val1
Debug.Print retArray(1)("key2")(0).GetText()    ' Inner Text
Debug.Print retArray(1)("key2")(1).GetText()    ' Inner Text
Debug.Print retArray(1)("key3") ' val3
```

# ❤️ Thanks

[VBA-JSON](https://github.com/VBA-tools/VBA-JSON) by Tim Hall, JSON converter for VBA helps me a lot to make HTTP client and this awesome library is included in the release under its license. Thank you!
