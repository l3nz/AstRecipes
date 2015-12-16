---
layout: post
title:  Day of Week Calculator
date:   2006-05-14 13:48:13.0
tags:
 - Calendar
 - Functions

categories: update
---

[Day of Week Calculator]({% post_url 2006-05-14-day-of-week-calculator %})

//**************************************************************
// Calendar Functions (place in extensions.ael, requires *1.2)
//**************************************************************
// Return the day of the week for any given date, 0=Sunday...
// YY is (4) digit year
// MM is (2) digit Month
// DD is (2) digit Day
// The day of the week is returned via ${Weekday}
// Example Call: Macro(Calendar_Weekday,2006,05,14,${DayofWeek})
//   Upon return ${DayofWeek} will contain the Weekday string.
//
macro Calendar_Weekday(YY,MM,DD,Weekday) {
  Set(wd=$[((${DD}+1) + ${MM}*2 + (${MM}+1)*3/5 + ${YY} +  {YY}/4 - ${YY}/100 + ${YY}/400) % 7](-NOTFOUND-((${DD}+1) + ${MM}*2 + (${MM}+1)*3/5 + ${YY} +  {YY}/4 - ${YY}/100 + ${YY}/400) % 7));
  switch (${wd}) {
    case 0:
      Set(Weekday=Sunday);
      break;
    case 1:
      Set(Weekday=Monday);
      break;
    case 2:
      Set(Weekday=Tuesday);
      break;
    case 3:
      Set(Weekday=Wednesday);
      break;
    case 4:
      Set(Weekday=Thursday);
      break;
    case 5:
      Set(Weekday=Friday);
      break;
    case 6:
      Set(Weekday=Saturday);
      break;
    default:
      Set(Weekday=Unknown);
  };
};

