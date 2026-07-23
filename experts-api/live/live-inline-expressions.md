---
title: Live Inline Expressions
category: Experts API - Live
summary: Reference for MSX live inline expressions used to display dynamic live data.
---

# Live Inline Expressions

Some live content properties support live inline expressions for adding formatted dates and times of the current state.

## Syntax

Expression syntax of live inline expressions.

| Expression | Example | Since Version | Description |
|---|---|---|---|
| `{creation:date:{LIVE_DATE_FORMAT}}` | `{creation:date:mm/dd/yy}` | **0.1.70** | Date of creation timestamp of the live object in live date format. The `{LIVE_DATE_FORMAT}` part must be replaced with the format. Please see [Live Date Format](#live-date-format) for possible values. |
| `{creation:time:{LIVE_TIME_FORMAT}}` | `{creation:time:hh:mm:ss}` | **0.1.70** | Time of creation timestamp of the live object in live time format. The `{LIVE_TIME_FORMAT}` part must be replaced with the format. Please see [Live Time Format](#live-time-format) for possible values. |
| `{creation:formatter:{LIVE_FORMATTER_ID}}` | `{creation:formatter:date_time}` | **0.1.160** | Date and/or time of creation timestamp of the live object in live formatter format. The `{LIVE_FORMATTER_ID}` part must be replaced with the formatter ID. Please see [Live Formatter ID](#live-formatter-id) for possible values. |
| `{from:date:{LIVE_DATE_FORMAT}}` | `{from:date:mm/dd/yy}` | **0.1.70** | Date of start timestamp of the live object in live date format. The `{LIVE_DATE_FORMAT}` part must be replaced with the format. Please see [Live Date Format](#live-date-format) for possible values.<br><br>**Note: This expression only returns a value if the `type` property of the live object has been set to `"schedule"` or `"lifetime"`.** |
| `{from:time:{LIVE_TIME_FORMAT}}` | `{from:time:hh:mm:ss}` | **0.1.70** | Time of start timestamp of the live object in live time format. The `{LIVE_TIME_FORMAT}` part must be replaced with the format. Please see [Live Time Format](#live-time-format) for possible values.<br><br>**Note: This expression only returns a value if the `type` property of the live object has been set to `"schedule"` or `"lifetime"`.** |
| `{from:formatter:{LIVE_FORMATTER_ID}}` | `{from:formatter:date_time}` | **0.1.160** | Date and/or time of start timestamp of the live object in live formatter format. The `{LIVE_FORMATTER_ID}` part must be replaced with the formatter ID. Please see [Live Formatter ID](#live-formatter-id) for possible values.<br><br>**Note: This expression only returns a value if the `type` property of the live object has been set to `"schedule"` or `"lifetime"`.** |
| `{to:date:{LIVE_DATE_FORMAT}}` | `{to:date:mm/dd/yy}` | **0.1.70** | Date of end timestamp of the live object in live date format. The `{LIVE_DATE_FORMAT}` part must be replaced with the format. Please see [Live Date Format](#live-date-format) for possible values.<br><br>**Note: This expression only returns a value if the `type` property of the live object has been set to `"schedule"` or `"lifetime"`.** |
| `{to:time:{LIVE_TIME_FORMAT}}` | `{to:time:hh:mm:ss}` | **0.1.70** | Time of end timestamp of the live object in live time format. The `{LIVE_TIME_FORMAT}` part must be replaced with the format. Please see [Live Time Format](#live-time-format) for possible values.<br><br>**Note: This expression only returns a value if the `type` property of the live object has been set to `"schedule"` or `"lifetime"`.** |
| `{to:formatter:{LIVE_FORMATTER_ID}}` | `{to:formatter:date_time}` | **0.1.160** | Date and/or time of end timestamp of the live object in live formatter format. The `{LIVE_FORMATTER_ID}` part must be replaced with the formatter ID. Please see [Live Formatter ID](#live-formatter-id) for possible values.<br><br>**Note: This expression only returns a value if the `type` property of the live object has been set to `"schedule"` or `"lifetime"`.** |
| `{now:date:{LIVE_DATE_FORMAT}}` | `{now:date:mm/dd/yy}` | **0.1.70** | Date of current timestamp in live date format. The `{LIVE_DATE_FORMAT}` part must be replaced with the format. Please see [Live Date Format](#live-date-format) for possible values. |
| `{now:time:{LIVE_TIME_FORMAT}}` | `{now:time:hh:mm:ss}` | **0.1.70** | Time of current timestamp in live time format. The `{LIVE_TIME_FORMAT}` part must be replaced with the format. Please see [Live Time Format](#live-time-format) for possible values. |
| `{now:formatter:{LIVE_FORMATTER_ID}}` | `{now:formatter:date_time}` | **0.1.160** | Date and/or time of current timestamp in live formatter format. The `{LIVE_FORMATTER_ID}` part must be replaced with the formatter ID. Please see [Live Formatter ID](#live-formatter-id) for possible values. |
| `{duration:position}` | `{duration:position}` | **0.1.70** | Duration of the live object in simple position format. |
| `{duration:time:{LIVE_DURATION_FORMAT}}` | `{duration:time:hh:mm:ss}` | **0.1.70** | Duration of the live object in live duration format. The `{LIVE_DURATION_FORMAT}` part must be replaced with the format. Please see [Live Duration Format](#live-duration-format) for possible values. |
| `{duration:text:{LIVE_DURATION_TEXT}}` | `{duration:text:hms}` | **0.1.70** | Duration of the live object as live duration text. The `{LIVE_DURATION_TEXT}` part must be replaced with the text syntax. Please see [Live Duration Text](#live-duration-text) for possible values. |
| `{progress:position}` | `{progress:position}` | **0.1.70** | Progress of the live object in simple position format. |
| `{progress:percentage}` | `{progress:percentage}` | **0.1.70** | Progress of the live object in percentage. |
| `{progress:time:{LIVE_DURATION_FORMAT}}` | `{progress:time:hh:mm:ss}` | **0.1.70** | Progress of the live object in live duration format. The `{LIVE_DURATION_FORMAT}` part must be replaced with the format. Please see [Live Duration Format](#live-duration-format) for possible values. |
| `{progress:text:{LIVE_DURATION_TEXT}}` | `{progress:text:hms}` | **0.1.70** | Progress of the live object as live duration text. The `{LIVE_DURATION_TEXT}` part must be replaced with the text syntax. Please see [Live Duration Text](#live-duration-text) for possible values. |
| `{countdown:position}` | `{countdown:position}` | **0.1.70** | Countdown of the live object in simple position format.<br><br>**Note: This expression returns for state `"coming"` and `"running"` the actual countdown value.** |
| `{countdown:percentage}` | `{countdown:percentage}` | **0.1.70** | Countdown of the live object in percentage.<br><br>**Note: This expression returns for state `"coming"` a fixed value (i.e. 100%) and in state `"running"` the actual countdown value if the type property of the live object has been set to `"schedule"` or `"lifetime"`.** |
| `{countdown:time:{LIVE_DURATION_FORMAT}}` | `{countdown:time:hh:mm:ss}` | **0.1.70** | Countdown of the live object in live duration format. The `{LIVE_DURATION_FORMAT}` part must be replaced with the format. Please see [Live Duration Format](#live-duration-format) for possible values.<br><br>**Note: This expression returns for state `"coming"` and `"running"` the actual countdown value.** |
| `{countdown:text:{LIVE_DURATION_TEXT}}` | `{countdown:text:hms}` | **0.1.70** | Countdown of the live object as live duration text. The `{LIVE_DURATION_TEXT}` part must be replaced with the text syntax. Please see [Live Duration Text](#live-duration-text) for possible values.<br><br>**Note: This expression returns for state `"coming"` and `"running"` the actual countdown value.** |
| `{overflow:position}` | `{overflow:position}` | **0.1.70** | Overflow of the live object in simple position format. |
| `{overflow:time:{LIVE_DURATION_FORMAT}}` | `{overflow:time:hh:mm:ss}` | **0.1.70** | Overflow of the live object in live duration format. The `{LIVE_DURATION_FORMAT}` part must be replaced with the format. Please see [Live Duration Format](#live-duration-format) for possible values. |
| `{overflow:text:{LIVE_DURATION_TEXT}}` | `{overflow:text:hms}` | **0.1.70** | Overflow of the live object as live duration text. The `{LIVE_DURATION_TEXT}` part must be replaced with the text syntax. Please see [Live Duration Text](#live-duration-text) for possible values. |
| `{txt:{COLOR}:{LIVE_INLINE_EXPRESSION}}` | `{txt:msx-red:creation:date:mm/dd/yy}`<br>`{txt:msx-green:creation:time:hh:mm:ss}`<br>`{txt:msx-yellow:duration:position}`<br>`{txt:msx-blue:duration:percentage}` | **0.1.70** | Colored live inline expression. The `{COLOR}` part must be replaced with a color value and the `{LIVE_INLINE_EXPRESSION}` part must be replaced with any live inline expression without the braces. |

## Live Date Format

| Format | Example | Since Version |
|---|---|---|
| `m\|d\|D\|M` | `1\|24\|Thu\|Jan` | **0.1.70** |
| `mm\|dd\|yy\|DD\|MM` | `01\|24\|19\|Thursday\|January` | **0.1.70** |
| `ddd\|yyyy` | `24th\|2019` | **0.1.70** |
| `mm/dd/yy` | `01/24/19` | **0.1.70** |
| `dd.mm.yy` | `24.01.19` | **0.1.70** |
| `yyyy-mm-dd` | `2019-01-24` | **0.1.70** |
| `MM d, yyyy` | `January 24, 2019` | **0.1.70** |
| `d. MM yyyy` | `24. January 2019` | **0.1.70** |
| `D mm/dd` | `Thu 01/24` | **0.1.70** |
| `D, dd.mm.` | `Thu, 24.01.` | **0.1.70** |
| `D mm/dd/yyyy` | `Thu 01/24/2019` | **0.1.70** |
| `D, dd.mm.yyyy` | `Thu, 24.01.2019` | **0.1.70** |
| `DD, MM d, yyyy` | `Thursday, January 24, 2019` | **0.1.70** |
| `DD, d. MM yyyy` | `Thursday, 24. January 2019` | **0.1.70** |

## Live Time Format

| Format | Example | Since Version |
|---|---|---|
| `h\|m\|s` | `14\|16\|4` | **0.1.70** |
| `h\|m\|s/ampm` | `2\|16\|4 PM` | **0.1.70** |
| `hh\|mm\|ss` | `14\|16\|04` | **0.1.70** |
| `hh\|mm\|ss/ampm` | `02\|16\|04 PM` | **0.1.70** |
| `hh:mm` | `14:16` | **0.1.70** |
| `h:mm/ampm` | `2:16 PM` | **0.1.70** |
| `hh:mm:ss` | `14:16:04` | **0.1.70** |
| `h:mm:ss/ampm` | `2:16:04 PM` | **0.1.70** |

## Live Duration Format

| Format | Example | Since Version |
|---|---|---|
| `h\|m\|s` | `1\|34\|0` | **0.1.70** |
| `hh\|mm\|ss` | `01\|34\|00` | **0.1.70** |
| `H\|M\|S` | `1\|34\|00` | **0.1.70** |
| `[h]\|[m]\|[s]` | `1\|34\|` | **0.1.70** |
| `h:m` | `1:34` | **0.1.70** |
| `hh:mm` | `01:34` | **0.1.70** |
| `h:m:s` | `1:34:0` | **0.1.70** |
| `hh:mm:ss` | `01:34:00` | **0.1.70** |
| `m:s` | `34:0` | **0.1.70** |
| `mm:ss` | `34:00` | **0.1.70** |
| `[H:]M:ss` | `1:34:00` | **0.1.70** |

## Live Duration Text

| Syntax | Example | Since Version |
|---|---|---|
| `dhms` | `1 hr 34 min` | **0.1.70** |
| `DHMS` | `0 days 1 hr 34 min 0 sec` | **0.1.70** |
| `Dhms` | `0 days 1 hr 34 min` | **0.1.70** |
| `hms` | `1 hr 34 min` | **0.1.70** |
| `hmS` | `1 hr 34 min 0 sec` | **0.1.70** |
| `hm` | `1 hr 34 min` | **0.1.70** |
| `ms` | `94 min` | **0.1.70** |
| `d` | `0 days` | **0.1.70** |
| `h` | `2 hr` | **0.1.70** |
| `m` | `94 min` | **0.1.70** |
| `s` | `5640 sec` | **0.1.70** |
| `~dhms` | `2 hr` | **0.1.74** |
| `~hms` | `2 hr` | **0.1.74** |
| `~ms` | `94 min` | **0.1.74** |
| `~s` | `5640 sec` | **0.1.74** |

## Live Formatter ID

| Formatter ID | Example | Since Version |
|---|---|---|
| `time` | `11:09 AM` | **0.1.160** |
| `time_long` | `11:09:38 AM` | **0.1.160** |
| `time_day` | `Wed, 11:09 AM` | **0.1.160** |
| `time_day_long` | `Wed, 11:09:38 AM` | **0.1.160** |
| `day` | `Wed 10/30` | **0.1.160** |
| `day_long` | `Wed 10/30/2024` | **0.1.160** |
| `day_full` | `Wednesday, October 30, 2024` | **0.1.160** |
| `day_time` | `Wed 10/30, 11:09 AM` | **0.1.160** |
| `day_time_long` | `Wed 10/30/2024, 11:09 AM` | **0.1.160** |
| `day_time_full` | `Wednesday, October 30, 2024, 11:09 AM` | **0.1.160** |
| `date` | `10/30/24` | **0.1.160** |
| `date_long` | `October 30, 2024` | **0.1.160** |
| `date_time` | `10/30/24, 11:09 AM` | **0.1.160** |
| `date_time_long` | `October 30, 2024, 11:09 AM` | **0.1.160** |

## See Also

- [Live Object](./live-object.md)
- [Live Examples](./live-examples.md)
- [Cookbook → Live data (EPG, progress, countdowns)](../../reference/cookbook.md#live-data-epg-progress-countdowns) — the live-clock `extension`/`headline` pattern built from `{now:date:...}`/`{now:time:...}`
- [Common Misconceptions → Date / time / duration format tokens](../../reference/common-misconceptions.md#date--time--duration-format-tokens) — lowercase/case-sensitive tokens, `MM`/`DD` are names not padded numbers, and the fixed duration-family list (also used by `{num:...}`)
