# Chrome Navigator

## Full Product & Feature Specification

---

# 1. Product Overview

Chrome Navigator là một **keyboard-first universal navigator cho Google Chrome**.

Mục tiêu chính là gom những dữ liệu đang bị phân tán trong Chrome thành một giao diện tìm kiếm duy nhất:

- Tabs đang mở
- Bookmarks
- Browser History
- Pinned items
- Recently used items
- Frequently used items
- Custom aliases
- Custom domain scopes
- Saved searches
- Browser actions
- Search engines
- Custom commands

Người dùng có thể gọi Navigator từ bất kỳ website nào bằng keyboard, nhập một vài ký tự, sau đó điều hướng tới đúng tài nguyên mà không cần mở:

- Chrome history page
- Bookmark manager
- Tab search
- Address bar
- Sidebar
- Extensions popup

Triết lý sản phẩm:

```text
Open → Type → Navigate
```

Mọi thứ phải tối ưu cho:

```text
Keyboard first
Minimal interaction
Fast response
Local-first
Privacy-first
No duplicate tabs
Low cognitive load
```

---

# 2. Core Product Concept

Chrome Navigator hoạt động giống sự kết hợp giữa:

```text
Chrome Omnibox
+
Raycast
+
Spotlight
+
Chrome Tab Search
+
Bookmark Manager
+
History Search
```

nhưng nằm trực tiếp trên trang hiện tại.

Ví dụ:

```text
Shift + O
```

xuất hiện:

```text
┌──────────────────────────────────────────────────────┐
│ Search tabs, bookmarks and history...                │
├──────────────────────────────────────────────────────┤
│ GitHub — my-project                            TAB   │
│ github.com/company/my-project                        │
│                                                      │
│ GitHub                                               │
│ github.com                                     BM    │
│                                                      │
│ GitHub pull request                                  │
│ github.com/company/project/pull/123          HISTORY │
└──────────────────────────────────────────────────────┘
```

Người dùng có thể thao tác hoàn toàn bằng keyboard.

---

# 3. Primary User Goals

Chrome Navigator phải giải quyết các nhu cầu sau.

## Find

Tìm nhanh:

```text
Tab đang mở
Bookmark
History
Pinned URL
Recent item
Frequently visited page
```

---

## Navigate

Đi tới tài nguyên mà không tạo duplicate tab.

---

## Scope

Thu hẹp phạm vi tìm kiếm.

Ví dụ:

```text
/tab react

/bm react

/history react
```

---

## Domain Search

Tìm trong một website cụ thể.

Ví dụ:

```text
/jira bug login

/gh react

YT music
```

---

## URL Behavior

Kiểm soát URL khi navigate.

Ví dụ:

```text
@query /jira login
```

giữ toàn bộ query.

Hoặc:

```text
@domain /jira login
```

chỉ navigate tới domain.

---

## Personalization

Người dùng có thể:

```text
pin
alias
prefix
search scope
ranking preference
URL behavior
keyboard shortcuts
```

---

# 4. Invocation

Navigator phải hỗ trợ nhiều phương thức gọi.

## Primary shortcut

```text
Shift + O
```

Chỉ kích hoạt khi người dùng không nhập liệu.

Không kích hoạt khi focus trong:

```text
input
textarea
select
contenteditable
textbox
searchbox
combobox
code editor
rich text editor
```

---

# 5. Browser Shortcut

Ngoài shortcut từ content script, extension phải có browser-level shortcut.

Ví dụ:

```text
Ctrl + Shift + O
```

Người dùng có thể customize.

---

# 6. Toggle Behavior

Nếu Navigator đang đóng:

```text
shortcut
→ open
```

Nếu đang mở:

```text
shortcut
→ close
```

---

# 7. Overlay Behavior

Navigator xuất hiện dạng floating overlay.

Không làm thay đổi layout website.

Không inject style gây ảnh hưởng website.

Không bị CSS website ảnh hưởng.

---

# 8. Overlay Position

Default:

```text
Horizontal center

Khoảng 15–25% từ top viewport
```

Không nhất thiết nằm chính giữa chiều cao vì command palettes thường dễ sử dụng hơn khi nằm phía trên center.

---

# 9. Overlay Dismiss

Có thể đóng bằng:

```text
Escape

click outside

global shortcut lần nữa
```

Optional setting:

```text
Close when page loses focus
```

---

# 10. Search Input

Input là trung tâm toàn bộ extension.

Khi Navigator mở:

```text
input auto focus
```

Không cần click.

---

# 11. Universal Search

Nhập text bình thường:

```text
react
```

search tất cả:

```text
Tabs
Bookmarks
History
Pins
Recent
```

---

# 12. Searchable Fields

Mỗi resource có thể được match qua:

```text
Title
URL
Hostname
Domain
Pathname
Query string
Bookmark folder
Alias
Tags
Custom keywords
```

---

# 13. Default Source Priority

Default logical priority:

```text
Open tabs
Pinned items
Bookmarks
History
```

Tuy nhiên priority không phải hard ordering.

Một history result cực kỳ relevant vẫn có thể nằm trên một tab không relevant.

---

# 14. Search Scope Commands

Built-in scopes.

## Tabs

```text
/tab
/t
```

Example:

```text
/tab jira
```

---

## Bookmark

```text
/bookmark
/bm
```

Example:

```text
/bm react
```

---

## History

```text
/history
/ht
```

Example:

```text
/ht github
```

---

## Pinned Items

Có thể hỗ trợ:

```text
/pin
/pinned
```

---

## Recent

Có thể hỗ trợ:

```text
/recent
```

---

# 15. Search Scope Combination

Scope có thể kết hợp với modifiers.

Example:

```text
@query /history jira
```

hoặc:

```text
/history @query jira
```

Token order không nên quá rigid.

---

# 16. Custom Domain Scope

User có thể tạo custom scope.

Example:

```text
/jira
```

mapping tới:

```text
jira.company.com
*.atlassian.net
```

---

# 17. Multiple Domains Per Scope

Một alias có thể map nhiều domains.

Example:

```text
/github
```

match:

```text
github.com
gist.github.com
githubusercontent.com
```

nếu user muốn.

---

# 18. Custom Prefix

Ngoài slash alias:

```text
/jira
```

user có thể định nghĩa:

```text
JIRA
JR
jira
jr
```

---

# 19. Prefix Case Sensitivity

Default:

```text
case-insensitive
```

Tức là:

```text
GH
gh
Gh
gH
```

được hiểu giống nhau.

---

# 20. Prefix Position

Default custom prefix chỉ được parse nếu nằm đầu query.

Example:

```text
GH react
```

match GitHub alias.

Nhưng:

```text
react GH hook
```

không được hiểu là alias.

Điều này tránh false positive.

---

# 21. Alias Conflict

Nếu custom alias conflict với system command:

```text
/tab
/history
/bm
```

system command thắng.

---

# 22. Alias Priority

Nếu có:

```text
/g
/gh
/github
```

thì longest matching token thắng.

---

# 23. Custom Alias Model

Một alias có thể gồm:

```text
Name

Triggers

Domains

URL patterns

Navigation preference

Search preference

Icon

Color

Enabled/disabled
```

Example:

```text
Name:
Jira

Triggers:
jira
jr
/jira
/jr

Domains:
jira.company.com
*.atlassian.net

Default navigation:
query
```

---

# 24. Domain Matching

Không match bằng string contains đơn giản.

Ví dụ:

```text
notgithub.com
```

không được match alias:

```text
github.com
```

Cần normalize bằng hostname.

---

# 25. URL Pattern Matching

Alias có thể hỗ trợ:

```text
domain
subdomain wildcard
pathname prefix
URL pattern
```

Example:

```text
github.com/company/*
```

---

# 26. Search Modifiers

Modifiers thay đổi hành vi search hoặc navigate.

Core modifiers:

```text
@query
@domain
```

---

# 27. @query

Ý nghĩa:

```text
preserve path
preserve query parameters
preserve relevant URL state
```

Example result:

```text
https://jira.com/issues?id=ABC-123&view=full
```

Navigate đúng URL đó.

---

# 28. @domain

Ý nghĩa:

Navigate về domain/root.

Example result:

```text
https://jira.com/issues?id=ABC-123
```

Navigate thành:

```text
https://jira.com/
```

---

# 29. Default Navigation Preference

User có thể đặt global default:

```text
Prefer exact URL

Prefer domain
```

Modifier override global preference.

Ví dụ global:

```text
Prefer query
```

nhưng query:

```text
@domain jira
```

thì domain thắng.

---

# 30. Alias-Level Navigation Preference

Mỗi alias có thể override global behavior.

Example:

```text
Jira:
Prefer query

Google:
Prefer domain
```

---

# 31. URL Normalization

System cần normalize URL cho:

```text
matching
deduplication
ranking
navigation
tab reuse
```

---

# 32. Tracking Parameter Handling

Có thể bỏ các tracking query params khỏi canonical identity.

Examples:

```text
utm_source
utm_medium
utm_campaign
utm_content
utm_term
fbclid
gclid
```

Nhưng URL thực tế vẫn có thể giữ nguyên khi navigate.

---

# 33. Hash Handling

Example:

```text
github.com/repo#readme
```

User setting:

```text
Treat fragment as same page
```

Default có thể ignore fragment khi detecting duplicate tab.

---

# 34. Exact Search

Search engine nên ưu tiên:

```text
exact title

exact domain

exact hostname

exact URL
```

---

# 35. Prefix Search

Example:

```text
git
```

match:

```text
GitHub
GitLab
GitBook
```

---

# 36. Contains Search

Example:

```text
invoice
```

match title/path/url có `invoice`.

---

# 37. Token Search

Example:

```text
jira login bug
```

phải tìm được:

```text
Login issue — Jira
jira.company.com/project/bug
```

dù thứ tự từ khác nhau.

---

# 38. Fuzzy Search

Optional:

```text
gthb
```

match:

```text
GitHub
```

Example:

```text
jra logn
```

match:

```text
Jira Login
```

---

# 39. Fuzzy Search Strength

User setting:

```text
Off
Low
Balanced
Aggressive
```

---

# 40. Typo Tolerance

Có thể hỗ trợ typo nhẹ.

Example:

```text
gihub
```

match GitHub.

---

# 41. Search Ranking

Ranking engine phải đánh giá nhiều tín hiệu.

Potential factors:

```text
Text relevance

Title match

Hostname match

URL match

Source type

Open tab status

Pinned status

Bookmark status

Recency

Visit frequency

User selections

Alias match

Current window

Current domain

Last used

Exact query history
```

---

# 42. Source Score

Default preference:

```text
Open Tab
>
Bookmark
>
History
```

nhưng không hard-coded absolutistically.

---

# 43. Current Window Boost

Nếu có hai tabs giống hoặc tương tự, tab trong current window có thể được ưu tiên.

---

# 44. Active Tab Penalty

Current active tab có thể:

```text
hide

hoặc

rank lower
```

vì navigate tới tab hiện tại thường không hữu ích.

Setting:

```text
Show current tab
```

---

# 45. Recent Boost

Recently accessed item có thể tăng ranking.

---

# 46. Frequency Boost

Một URL user thường xuyên mở có thể tăng ranking.

---

# 47. Learned Ranking

Extension có thể học local từ hành vi.

Example:

User thường search:

```text
jira
```

và chọn:

```text
jira.company.com/secure/RapidBoard.jspa
```

Sau một thời gian item đó lên đầu.

Không cần AI.

---

# 48. Query-Specific Learning

Ranking có thể ghi nhớ:

```text
query → selected URL
```

Example:

```text
"prod"
→ production dashboard
```

---

# 49. Decay

Usage ranking không nên tăng vô hạn.

Old behavior phải giảm weight theo thời gian.

---

# 50. Search Result Types

Base result types:

```text
Tab

Bookmark

History

Pinned URL

Recent

Command

Search suggestion

Custom action
```

---

# 51. Unified Result

Một URL có thể tồn tại đồng thời trong:

```text
Tab
Bookmark
History
Pin
```

Không nên hiển thị 4 rows giống nhau.

---

# 52. Result Deduplication

Example:

```text
github.com/company/project
```

exists as:

```text
open tab
bookmark
history
```

show một result.

Metadata:

```text
TAB
BOOKMARKED
PINNED
```

---

# 53. Deduplication Strategy

Có thể support:

```text
Exact URL
Ignore tracking params
Ignore query
Path-level
Domain-level
```

---

# 54. Result Row

Một result tiêu chuẩn nên chứa:

```text
favicon

title

URL

source

optional status

keyboard hint
```

---

# 55. URL Display

Có thể simplify URL display.

Example:

```text
https://github.com/company/project?tab=readme
```

render:

```text
github.com/company/project?tab=readme
```

---

# 56. Match Highlight

Search:

```text
react
```

UI highlight phần match trong:

```text
title
domain
path
```

---

# 57. Result Source Badge

Examples:

```text
TAB

BM

HISTORY

PIN
```

---

# 58. Open Tab Indicator

Nếu item là bookmark/history nhưng hiện đang mở:

```text
OPEN
```

---

# 59. Current Window Indicator

Optional:

```text
CURRENT WINDOW
```

---

# 60. Window Information

Result tab có thể hiển thị:

```text
Window 1

Window 2

Incognito
```

nếu cần.

---

# 61. Keyboard Navigation

Core controls:

```text
Tab
→ next

ArrowDown
→ next

Shift + Tab
→ previous

ArrowUp
→ previous

Enter
→ open/focus

Escape
→ close
```

---

# 62. Keyboard Focus Model

Input nên giữ actual DOM focus.

Result selection nên được quản lý qua:

```text
activeIndex
```

thay vì chuyển DOM focus qua từng result.

Lợi ích:

User tiếp tục type ngay lập tức.

---

# 63. Selection Wrap

Setting:

```text
Wrap navigation
```

Nếu bật:

```text
last result + ArrowDown
→ first result
```

---

# 64. Home / End

Optional:

```text
Home
→ first result

End
→ last result
```

---

# 65. Open Behavior

Enter:

```text
reuse existing tab if possible
otherwise create tab
```

---

# 66. Existing Tab Detection

Before navigating:

```text
search open tabs
```

Nếu destination đã mở:

```text
focus window

activate tab
```

Không tạo duplicate.

---

# 67. Existing Tab Match Strategies

User có thể chọn:

```text
Exact URL

Ignore query

Same path

Same origin

Same domain
```

---

# 68. Exact Match

Example:

```text
https://jira.com/a?id=1
```

chỉ match cùng exact destination.

---

# 69. Ignore Query

Example:

```text
jira.com/a?id=1
jira.com/a?id=2
```

được coi cùng page.

---

# 70. Same Path

Example:

```text
jira.com/a?id=1
jira.com/a?id=2
```

same pathname:

```text
/a
```

---

# 71. Same Origin

Example:

```text
https://jira.com/a
https://jira.com/b
```

có thể reuse cùng tab nếu setting cho phép.

---

# 72. Same Domain

Potentially reuse:

```text
a.example.com
b.example.com
```

nếu user explicitly chọn behavior này.

---

# 73. Force New Tab

Shortcut:

```text
Alt + Enter
```

Behavior:

```text
always create new tab
```

bỏ qua existing-tab detection.

---

# 74. Background Tab

Shortcut:

```text
Shift + Enter
```

Behavior:

```text
open background tab
keep current tab active
```

---

# 75. Open New Window

Potential action:

```text
Ctrl + Shift + Enter
```

hoặc action menu.

---

# 76. Incognito

Nếu permission cho phép:

```text
Open in incognito window
```

---

# 77. Result Click

Mouse click có behavior giống Enter.

Middle click:

```text
open background/new tab
```

---

# 78. Pin Feature

Shortcut:

```text
Alt + P
```

toggles pin.

---

# 79. Pin Semantics

Pinned item có thể represent:

```text
Exact URL

Domain

Search

Command
```

---

# 80. URL Pin

Example:

```text
github.com/company/project
```

---

# 81. Domain Pin

Example:

```text
github.com
```

---

# 82. Search Pin

Example:

```text
/jira assigned to me
```

Pinned saved query.

---

# 83. Command Pin

Example:

```text
Close duplicate tabs
```

nếu command system được thêm.

---

# 84. Empty Query

Khi mở Navigator chưa nhập gì, không nên để blank.

Có thể hiển thị:

```text
Pinned

Recent

Frequently used

Open tabs
```

---

# 85. Pinned Section

Pinned items nằm đầu.

---

# 86. Recent Section

Recent selections trong Navigator.

Không nhất thiết giống browser history.

---

# 87. Recently Closed

Potential result source:

```text
Recently Closed Tabs
```

---

# 88. Frequent Section

Items user hay dùng.

---

# 89. Current Context

Potential contextual suggestions:

Nếu đang ở GitHub:

```text
other GitHub tabs

same repo pages
```

Nếu đang ở Jira:

```text
other Jira tabs
```

---

# 90. Search History

Navigator có thể lưu các query gần đây.

Example:

```text
jira production

github react

docs api
```

---

# 91. Search History Controls

User có thể:

```text
clear recent search

disable search history

limit count
```

---

# 92. Saved Search

User có thể lưu query.

Example:

```text
/jira project ABC
```

saved as:

```text
ABC Jira
```

---

# 93. Saved Search Invocation

Could become:

```text
/abc
```

---

# 94. Search Engine Fallback

Nếu không có result phù hợp:

```text
nestjs cronjob gcp
```

show:

```text
Search Google for "nestjs cronjob gcp"
```

---

# 95. Custom Search Engines

User có thể configure:

```text
Google
DuckDuckGo
Bing
Brave
Kagi
Perplexity
custom
```

---

# 96. Search Engine Prefixes

Examples:

```text
g chrome extension api

ddg privacy extensions

yt lo-fi music
```

---

# 97. URL Templates

Một alias có thể trở thành URL launcher.

Example:

```text
jira ABC-123
```

template:

```text
https://jira.company.com/browse/{query}
```

result:

```text
Open ABC-123 in Jira
```

---

# 98. GitHub URL Template

Example:

```text
gh facebook/react
```

→

```text
https://github.com/facebook/react
```

---

# 99. Search URL Template

Example:

```text
gh react server components
```

could offer:

```text
https://github.com/search?q=react+server+components
```

---

# 100. Alias Action Types

Custom alias can support action type:

```text
Filter existing resources

Open URL template

Run search engine

Run command
```

---

# 101. Commands Mode

Potential syntax:

```text
>
```

Example:

```text
> close duplicate tabs
```

---

# 102. Browser Commands

Potential commands:

```text
Close tab

Close duplicate tabs

Close tabs to the right

Close other tabs

Reopen closed tab

Mute current tab

Mute all tabs

Pin Chrome tab

Unpin Chrome tab

Duplicate tab

Bookmark current page

Bookmark all tabs

Reload current tab

Reload all tabs
```

---

# 103. Tab Management

Search result action menu can include:

```text
Switch to tab

Close tab

Duplicate tab

Mute

Pin Chrome tab

Move to window

Move to new window
```

---

# 104. Multi-Select

Future advanced mode:

```text
select multiple tabs
```

actions:

```text
close

move

bookmark

mute
```

---

# 105. Window Search

Scope:

```text
/window
/w
```

could show browser windows.

---

# 106. Window Filter

Examples:

```text
/window github
```

show windows containing GitHub.

---

# 107. Current Window Scope

Possible:

```text
@window
```

search only current browser window.

---

# 108. Other Window Scope

Potential filter by:

```text
Window 1
Window 2
```

---

# 109. Tab Groups

Chrome tab groups can become searchable entities.

Potential:

```text
/group
```

---

# 110. Tab Group Actions

Possible:

```text
Switch group

Collapse group

Expand group

Close group

Move tab into group
```

---

# 111. Bookmark Search

Bookmark search should support:

```text
Title

URL

Folder name

Folder hierarchy
```

---

# 112. Bookmark Folder Display

Example:

```text
Development / GitHub / Work
```

---

# 113. Bookmark Actions

Possible:

```text
Open

Open new tab

Copy URL

Edit bookmark

Remove bookmark

Open folder
```

---

# 114. History Search

History result data:

```text
Title

URL

Last visit

Visit count

Typed count
```

---

# 115. History Grouping

Setting:

```text
No grouping

Exact URL

Path

Domain

Smart
```

---

# 116. Smart History Grouping

Example:

```text
example.com/product?id=1
example.com/product?id=2
```

could be grouped depending on semantics.

---

# 117. History Actions

Possible:

```text
Open

Open domain

Copy URL

Remove history item

Show visits
```

---

# 118. Query Parameter Rules

User can specify global rules.

Example:

```text
Ignore:
utm_*
fbclid
gclid
```

---

# 119. Domain-Specific Query Rules

Example Jira:

```text
Keep:
selectedIssue
rapidView

Ignore:
utm_source
```

---

# 120. Domain Navigation Rules

Example:

```text
Jira
default = query

GitHub
default = exact URL

Google
default = domain
```

---

# 121. Custom URL Rules

Advanced users can define:

```text
match domain

remove params

keep params

strip fragment

rewrite path
```

---

# 122. Copy URL

Shortcut:

```text
Ctrl/Cmd + C
```

when result selected.

---

# 123. Copy Title

Possible:

```text
Ctrl/Cmd + Shift + T
```

---

# 124. Copy Markdown

Output:

```markdown
[Project](https://github.com/company/project)
```

---

# 125. Copy Formats

User-configurable:

```text
URL

Title

Title + URL

Markdown

HTML
```

---

# 126. Actions Menu

Selected item can expose actions.

Trigger:

```text
Ctrl + K

or

ArrowRight
```

Example:

```text
Open

Open new tab

Open background tab

Copy URL

Copy Markdown

Pin

Remove from history
```

---

# 127. Action Search

Action menu itself can be searchable.

---

# 128. Mouse Support

Although keyboard-first, basic mouse support required:

```text
hover

click

scroll

action click
```

---

# 129. Scroll Behavior

When active item moves beyond viewport:

```text
auto-scroll into view
```

---

# 130. Result Limit

Setting:

```text
5
10
20
50
```

---

# 131. Progressive Results

Could render fast sources first:

```text
Tabs
Bookmarks cache
```

then slower:

```text
History
```

without resetting selection unexpectedly.

---

# 132. Selection Stability

If new results arrive asynchronously:

```text
active item should remain selected
```

if possible.

Do not suddenly move user's selection.

---

# 133. Search Loading State

Possible subtle indicator:

```text
Searching history…
```

Avoid intrusive spinners.

---

# 134. No Results State

Example:

```text
No browser results

Search Google for "..."
```

---

# 135. Permission Missing State

Example:

```text
History search is disabled

Enable History Search
```

---

# 136. Search Error State

One failed source should not break everything.

Example:

```text
Tabs ✓

Bookmarks ✓

History unavailable
```

---

# 137. Settings

Settings categories may include:

```text
General

Search

Ranking

Navigation

Aliases

URLs

Keyboard

Appearance

Privacy

Permissions

Data
```

---

# 138. General Settings

Examples:

```text
Open on startup

Close after navigation

Remember previous query

Clear query on close

Show current tab

Result limit
```

---

# 139. Search Settings

```text
Enable tabs

Enable bookmarks

Enable history

Enable fuzzy search

Fuzzy strength

Search query parameters

Search bookmark folders
```

---

# 140. Source Priority Settings

Advanced user may reorder:

```text
Tabs

Bookmarks

History

Pinned
```

---

# 141. Ranking Settings

Could expose:

```text
Prefer open tabs

Prefer recent

Prefer frequent

Prefer exact domain

Use learning
```

---

# 142. Navigation Settings

```text
Default URL mode

Existing tab match strategy

Focus existing window

Open background behavior

Strip tracking params
```

---

# 143. Keyboard Settings

Configurable shortcuts:

```text
Open Navigator

Next result

Previous result

Open

Force new tab

Background tab

Pin

Copy URL

Actions
```

---

# 144. Appearance

Possible settings:

```text
System

Light

Dark
```

---

# 145. Density

```text
Compact

Comfortable
```

---

# 146. Overlay Width

```text
Small

Medium

Large

Custom
```

---

# 147. Result Detail Level

```text
Title only

Title + domain

Title + full URL
```

---

# 148. Favicon

Setting:

```text
Show favicons
```

---

# 149. Source Badges

Setting:

```text
Show source badges
```

---

# 150. Animations

```text
On

Reduced

Off
```

Respect:

```text
prefers-reduced-motion
```

---

# 151. Theme

Optional future custom themes.

---

# 152. Privacy

Strong product principle:

```text
Browser data remains local.
```

Unless user explicitly enables some remote integration.

---

# 153. History Privacy

Do not transmit:

```text
history URLs

searches

bookmark data

tab URLs
```

to external servers by default.

---

# 154. Learning Privacy

Usage learning should stay local.

---

# 155. Analytics

If analytics added:

Never include raw:

```text
URL

title

query

domain
```

without explicit informed consent.

Safer analytics:

```text
feature used

latency bucket

error code

result source
```

---

# 156. Data Management

Settings page should allow:

```text
Clear learned ranking

Clear Navigator search history

Clear pinned items

Reset aliases

Reset all settings
```

---

# 157. Export

User can export configuration.

Example:

```json
{
  "aliases": [],
  "settings": {},
  "pins": []
}
```

---

# 158. Import

Import configuration file.

---

# 159. Sync

Small config can optionally sync across Chrome profiles/devices.

Examples:

```text
aliases
preferences
keyboard config
```

Large data should remain local.

---

# 160. Pin Sync

User setting:

```text
Sync pins
```

---

# 161. Usage Learning Sync

Default:

```text
local only
```

---

# 162. Accessibility

Navigator should support:

```text
screen reader

keyboard only

high contrast

reduced motion

ARIA listbox/combobox behavior
```

---

# 163. Screen Reader

Announce:

```text
number of results

selected result

source

title

domain
```

---

# 164. Keyboard Trap

While Navigator is open:

```text
Tab
```

must navigate Navigator instead of escaping into website.

---

# 165. IME

Must behave correctly with:

```text
Vietnamese

Japanese

Chinese

Korean
```

Enter during composition must not navigate accidentally.

---

# 166. Website Compatibility

Navigator should work on normal:

```text
HTTP

HTTPS
```

pages.

---

# 167. Restricted Pages

Extension cannot behave identically on every Chrome internal page.

Product should gracefully handle unsupported pages rather than fail silently.

---

# 168. SPA Compatibility

Should work on applications using:

```text
React

Vue

Angular

Next.js

Nuxt

Svelte
```

---

# 169. Editor Compatibility

Global hotkey should not hijack typing in:

```text
Monaco

CodeMirror

ProseMirror

TipTap

Slate

Notion-like editors
```

---

# 170. Shadow DOM Compatibility

Navigator UI must remain visually isolated from site CSS.

---

# 171. Z-Index

Overlay must be above most website content.

Need robust strategy for sites with aggressive z-index usage.

---

# 172. Fullscreen

Define behavior for:

```text
browser fullscreen

video fullscreen

presentation fullscreen
```

Potential setting:

```text
Allow Navigator in fullscreen
```

---

# 173. Iframes

Keyboard handling should account for focus inside iframe.

Cross-origin iframe behavior may have limitations.

---

# 174. Multiple Chrome Windows

Tabs across all windows should be searchable.

---

# 175. Window Focus

If result belongs to another window:

```text
focus that window
activate tab
```

---

# 176. Incognito

Need explicit behavior depending on extension permission:

```text
include incognito tabs

exclude incognito tabs
```

---

# 177. Pinned Browser Tabs

Chrome-native pinned tabs can receive ranking boost.

---

# 178. Audible Tabs

Search result can indicate:

```text
playing audio
muted
```

---

# 179. Discarded Tabs

Could indicate:

```text
sleeping/discarded
```

---

# 180. Tab Context Metadata

Potential fields:

```text
window

group

pinned

audible

muted

last accessed
```

---

# 181. Performance

Navigator must feel instant.

Important metrics:

```text
time to open

time to first result

time to complete result set

keystroke latency

navigation latency
```

---

# 182. UI Response

Keyboard interaction should ideally fit within one frame.

---

# 183. Large Tab Count

Should remain usable with:

```text
500+ tabs
```

---

# 184. Large Bookmark Count

Should support:

```text
10,000+
```

bookmarks without UI freezing.

---

# 185. Large History

Must not attempt to preload entire browser history unnecessarily.

---

# 186. Search Cancellation

Rapid typing:

```text
g

gi

git

gith

github
```

older async searches should not overwrite latest search results.

---

# 187. Race Conditions

Handle:

```text
tab closes during search

bookmark deleted

window closes

history entry removed
```

between result render and Enter.

---

# 188. Debounce

History search may be debounced separately from tabs.

---

# 189. Cache

Possible caches:

```text
bookmark index

alias index

settings

favicons
```

---

# 190. Service Worker Lifecycle

Product behavior cannot rely solely on persistent background memory.

All critical state must survive worker restart.

---

# 191. Search Session

Each open Navigator instance can have:

```text
session ID

query

active item

request version
```

for race-condition control.

---

# 192. URL Safety

Never execute:

```text
javascript:
```

URLs.

---

# 193. Protocol Support

Default:

```text
http
https
```

Potential optional support:

```text
file
ftp-like custom handlers
chrome-extension
```

subject to browser restrictions.

---

# 194. Query Parser

Parser should support structured tokens.

Concept:

```text
[modifiers] [scope] [alias] [query]
```

---

# 195. Parser Example

Input:

```text
@domain /jira login bug
```

Parsed:

```text
Navigation mode:
domain

Alias:
jira

Query:
login bug
```

---

# 196. Unknown Slash Command

Input:

```text
/foo bar
```

Possible behavior:

```text
If alias exists:
use alias

Otherwise:
treat "/foo bar" as normal text
```

Avoid hard error.

---

# 197. Escaped Tokens

Advanced future behavior:

```text
\/jira
```

could search literal `/jira`.

---

# 198. Quoted Search

Future:

```text
"login bug"
```

means exact phrase.

---

# 199. Negative Search

Potential future:

```text
jira -dashboard
```

exclude dashboard.

---

# 200. Domain Filter Syntax

Possible advanced grammar:

```text
site:github.com react
```

---

# 201. Source Filter Syntax

Alternative syntax:

```text
source:tab
source:history
```

Could coexist with slash commands for power users.

---

# 202. Search Suggestions

As user types:

```text
/ji
```

suggest:

```text
/jira
```

---

# 203. Command Suggestions

Type:

```text
/
```

show available scopes/aliases.

---

# 204. Modifier Suggestions

Type:

```text
@
```

show:

```text
@query
@domain
@window
```

---

# 205. Help Mode

Possible command:

```text
/help
```

shows syntax.

---

# 206. Inline Help

Footer can show:

```text
↑↓ Navigate
↵ Open
Alt+P Pin
Esc Close
```

---

# 207. Contextual Help

If query starts `/`:

show available slash commands.

---

# 208. First-Run Experience

First open could teach:

```text
Shift + O

Type anything

Use arrows

Press Enter
```

Minimal onboarding.

---

# 209. Permission Onboarding

Permissions should be explained separately.

Example:

```text
Tabs:
used to find and switch existing tabs

Bookmarks:
used to search your saved bookmarks

History:
used to search previously visited pages
```

---

# 210. Optional History

History could be opt-in.

Navigator still usable with:

```text
tabs

bookmarks
```

only.

---

# 211. Permission Revocation

If user revokes History:

```text
/history
```

should show meaningful message.

---

# 212. First-Class Pins

Pins should remain usable even if source bookmark/history disappears.

---

# 213. Broken Pin

If pinned URL is invalid/unreachable, still show it unless user removes it.

---

# 214. Pin Editing

User can edit:

```text
title

URL

alias

navigation mode
```

---

# 215. Custom Display Name

Pinned/alias items can have display title independent of page title.

Example:

```text
Production
```

instead of:

```text
Dashboard - MyCompany
```

---

# 216. Tags

Optional future:

```text
work

personal

dev

prod
```

Pinned/bookmarked resources can have local tags.

---

# 217. Tag Search

Example:

```text
#work
```

---

# 218. Custom Collections

Potential future grouping:

```text
Work

Personal

Development

Research
```

---

# 219. Workspace

More advanced future abstraction:

```text
Workspace
=
tabs
URLs
aliases
commands
```

Example:

```text
Open "Frontend Work"
```

restores set of pages.

---

# 220. Session Restore

Navigator could save tab sessions.

Example:

```text
Save current window as "Project A"
```

---

# 221. Session Search

```text
/session project
```

---

# 222. URL Launcher

If input itself is valid URL:

```text
example.com
```

show:

```text
Open example.com
```

---

# 223. Domain Autocomplete

Example:

```text
git
```

could suggest frequent domain:

```text
github.com
```

---

# 224. Direct Navigation

If exact domain typed:

```text
github.com
```

could directly offer navigate action.

---

# 225. Calculator

Potential command-palette expansion:

```text
= 10 * 25
```

but outside navigation core.

---

# 226. Notes / Clipboard

Potential future expansion, but not core product.

Keep architecture open but avoid coupling core search with unrelated utilities.

---

# 227. Browser Search Replacement

Long-term extension could optionally replace some use cases of:

```text
Ctrl + L

Ctrl + Shift + A
```

without trying to override browser UX completely.

---

# 228. Omnibox Integration

Potential Chrome extension omnibox keyword.

Example:

```text
nav github
```

inside Chrome address bar.

---

# 229. Context Menu

Optional:

```text
Pin current page to Navigator

Create alias for domain

Search this domain
```

---

# 230. Page Quick Action

Potential shortcut:

```text
Pin current page
```

without opening Navigator.

---

# 231. Auto Alias Suggestion

If user frequently searches same domain:

```text
Would you like to create alias "jira"?
```

Should be non-intrusive and optional.

---

# 232. Duplicate Tab Detection

Navigator can expose duplicate tabs.

Example:

```text
Duplicates
github.com/project × 4
```

---

# 233. Duplicate Tab Command

Potential:

```text
> close duplicate tabs
```

---

# 234. Tab Cleanup

Possible actions:

```text
Close tabs older than X

Close inactive tabs

Close duplicate tabs
```

Should remain optional advanced functionality.

---

# 235. Current Domain Search

Shortcut modifier:

```text
@current
```

Search resources matching current domain.

---

# 236. Current Project Context

Could infer repo/project from URL paths.

Example:

```text
github.com/company/project
```

and boost pages within same repo.

Optional advanced feature.

---

# 237. Smart Domain Names

Display:

```text
GitHub
```

instead of only:

```text
github.com
```

based on known/favicon/title metadata.

---

# 238. Page Metadata

Potential metadata:

```text
title

favicon

hostname

site name

last visit

visit count

open status
```

---

# 239. Result Preview

Optional side panel preview:

```text
full URL

metadata

bookmark folder

last visited

visit count
```

Not necessary for minimal UI.

---

# 240. Keyboard Command Discoverability

Footer updates depending on selected source.

For tab:

```text
Enter Switch
Alt+Enter New
Alt+W Close
```

For history:

```text
Enter Open
Alt+P Pin
```

---

# 241. Command Palette Modes

Possible top-level modes:

```text
Search

Commands

Actions

Settings
```

---

# 242. Settings Search

Possible command:

```text
/settings
```

or:

```text
> settings
```

---

# 243. Alias Search

Possible:

```text
/aliases
```

manage custom aliases.

---

# 244. Search Debug Mode

Developer setting could show score:

```text
GitHub
score: 183.2
```

Useful while tuning ranking.

---

# 245. Ranking Explainability

Potential advanced option:

```text
Why is this result ranked here?
```

Show:

```text
Exact hostname +100

Open tab +60

Recent +20
```

Useful for debugging and power users.

---

# 246. Internal Search Index

Architecture may eventually maintain lightweight indexes for:

```text
bookmarks

pins

aliases

usage
```

History can remain dynamic.

---

# 247. Feature Flags

Useful for experimenting with:

```text
fuzzy ranking

learning

commands

new parser
```

---

# 248. Experimental Settings

Advanced settings category:

```text
Experimental
```

for unfinished features.

---

# 249. Backup

Export config as JSON.

Optional auto backup via browser sync.

---

# 250. Reset

Levels:

```text
Reset search learning

Reset aliases

Reset appearance

Reset everything
```

---

# 251. Localization

Architecture should support future translations.

Initial language can be English.

Potential:

```text
English

Vietnamese
```

---

# 252. Date Formatting

History timestamps use local locale.

---

# 253. URL Internationalization

Support Unicode domains and non-English titles correctly.

---

# 254. Search Unicode

Search must work with:

```text
Vietnamese accents

Chinese

Japanese

Korean

Cyrillic
```

---

# 255. Accent Normalization

Optional setting:

```text
Accent-insensitive search
```

Example:

```text
cong nghe
```

may match:

```text
công nghệ
```

Potentially useful for Vietnamese.

---

# 256. Search Case

Default:

```text
case-insensitive
```

---

# 257. URL Decode

Search should potentially match decoded path.

Example:

```text
/My%20Documents
```

search:

```text
My Documents
```

---

# 258. Punycode

Normalize domain display/search where appropriate.

---

# 259. Result Identity

Every result requires stable identity independent of array index.

Important for:

```text
selection stability

pin

learning

async results
```

---

# 260. Navigation Logging

Local usage event may include:

```text
query normalized

selected canonical URL

timestamp

source
```

Only if learning enabled.

---

# 261. Learning Controls

Settings:

```text
Enable smart ranking

Forget learned behavior
```

---

# 262. Private Mode

Potential setting:

```text
Private Search Session
```

Search works but no Navigator usage records stored.

---

# 263. Hotkey Conflict

If `Shift+O` conflicts with website shortcuts:

Potential behavior:

```text
disable on specific domain

custom shortcut per site
```

---

# 264. Domain Exclusion

User can define excluded sites.

Example:

```text
docs.google.com
figma.com
```

where page-level Shift+O should not activate.

Browser-level shortcut can still work.

---

# 265. Domain Shortcut Override

Advanced:

```text
github.com:
Shift + O disabled

jira.com:
Alt + Space
```

---

# 266. Overlay Persistence

Default:

```text
close after navigate
```

Possible setting:

```text
Keep open after action
```

useful for multi-tab management.

---

# 267. Multi Action Mode

If keep-open enabled:

```text
close tab

next result

close tab

next result
```

possible without reopening Navigator.

---

# 268. Search Context Preservation

Setting:

```text
remember previous query
```

Default could be disabled.

---

# 269. Query Clear Behavior

Possible:

```text
Escape with query
→ clear query

Escape when empty
→ close
```

Alternative:

```text
Escape always close
```

User-configurable.

---

# 270. Backspace Navigation

If query empty:

```text
Backspace
```

could go back from action menu to results.

---

# 271. Nested Palette

Actions/settings can use nested command palette navigation.

---

# 272. Breadcrumb

Potential:

```text
Search > GitHub > Actions
```

---

# 273. Search Categories

Optional visual grouping:

```text
Tabs

Bookmarks

History
```

---

# 274. Flat Ranking

Alternative UI:

Single globally ranked list.

Default likely flat list because faster to navigate.

Setting could allow grouped results.

---

# 275. Grouped View

Example:

```text
TABS
...

BOOKMARKS
...

HISTORY
...
```

---

# 276. Maximum Per Source

Settings:

```text
Tabs 5

Bookmarks 5

History 10
```

---

# 277. Source Diversity

Ranking can avoid history overwhelming list.

Example:

Always reserve top slots for relevant tabs/bookmarks.

---

# 278. Search Quality Heuristics

Potential boosts:

```text
word boundary match

hostname start match

path segment match

exact acronym

title prefix
```

---

# 279. Acronym Matching

Example:

```text
SO
```

could match:

```text
Stack Overflow
```

if learned/custom configured.

---

# 280. Custom Keywords

Alias/resource can contain keywords.

Example:

```text
Production Dashboard

keywords:
prod
production
monitor
```

---

# 281. Bookmark Tagging

Local metadata can extend browser bookmarks without modifying Chrome bookmarks.

---

# 282. Pinned Order

User can manually reorder pins.

---

# 283. Pin Hotkeys

Advanced:

```text
Alt + 1

Alt + 2
```

launch first pinned resources.

---

# 284. Quick Slots

Potential feature:

```text
1 → GitHub

2 → Jira

3 → Production
```

when Navigator opens.

---

# 285. Favorite Aliases

Frequently used aliases appear when typing:

```text
/
```

---

# 286. Query Autocomplete

Example:

```text
/jira PRO
```

could suggest previously used:

```text
PROJ-123

PROJ-456
```

based on local history.

---

# 287. Search Suggestion Privacy

All autocomplete based on browser/local data by default.

---

# 288. External Integrations

Potential future integrations:

```text
Jira API

GitHub API

Linear API

Notion API
```

but these should be extensions of architecture rather than required for core Navigator.

---

# 289. Integration Result Type

Could introduce:

```text
RemoteResource
```

alongside:

```text
Tab
Bookmark
History
```

---

# 290. Integration Scope

Examples:

```text
/jira issue

/gh repo

/linear issue
```

could eventually search remote services.

---

# 291. Offline Behavior

Core functionality must work offline for:

```text
tabs

bookmarks

local history

pins

aliases
```

---

# 292. Network Independence

Navigator should not block opening because network unavailable.

---

# 293. Update Safety

Extension updates must not wipe:

```text
pins

aliases

settings

learned ranking
```

---

# 294. Settings Migration

Schema should support version migration.

Example:

```text
settingsVersion
```

---

# 295. Data Schema Version

Persistent storage models should include migration path.

---

# 296. Error Recovery

Corrupt settings should fall back to safe defaults without crashing Navigator.

---

# 297. Invalid Alias

Alias with invalid domain should be marked invalid in settings rather than break parser.

---

# 298. Duplicate Alias

Settings should detect duplicate triggers.

---

# 299. Dangerous Shortcut

Prevent assigning same internal shortcut to incompatible actions.

---

# 300. Product Identity

Core identity should remain:

> One keyboard shortcut to find and navigate anything already inside your browser.

The strongest product differentiators should be:

```text
Universal Chrome search

Smart existing-tab reuse

Custom aliases

Domain-aware navigation

Query/domain URL control

Local smart ranking

Keyboard-first UX
```

The product should avoid becoming a generic productivity launcher too early.

Everything else — commands, integrations, workspaces, tab cleanup, search engines — should remain built around the central concept:

```text
Find
→ Decide destination
→ Reuse when possible
→ Navigate instantly
```
