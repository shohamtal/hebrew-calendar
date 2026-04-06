# תזכורות לוח עברי

אפליקציית web להוספת אירועים ליומן גוגל לפי הלוח העברי.

**[פתח את האפליקציה](https://shohamtal.github.io/hebrew-calendar/)**

## פיצ'רים

### ספירת העומר
- בחירת שנים מ-10 השנים הקרובות
- 49 תזכורות יומיות בשעה שנבחרת (ברירת מחדל 20:00)
- נוסח מלא בעברית: "היום X ימים בעומר שהם Y שבועות ו-Z ימים"
- תפילת "הרחמן הוא יחזיר לנו עבודת בית המקדש..."

### תאריך עברי
- הזנת תאריך עברי (יום בגימטריה + חודש)
- טקסט תזכורת חופשי (למשל: "יום הולדת שוהם")
- חישוב אוטומטי של התאריך הלועזי לכל שנה מ-10 הקרובות
- שימושי לימי הולדת, יום זיכרון, יארצייט וכו'

### תצוגה מקדימה
כל האירועים מוצגים בטבלה לפני היצירה - כולל תאריך, יום בשבוע, וטקסט - כדי לוודא שהחישוב נכון לפני ההוספה ליומן.

## ארכיטקטורה

```
index.html (קובץ בודד, ללא שרת)
    │
    ├── @hebcal/core (CDN) ── המרת תאריכים עברי ↔ לועזי
    ├── Google Identity Services ── התחברות OAuth
    └── Google Calendar API ── יצירת אירועים ביומן
```

- **ללא צד שרת** - הכל רץ בדפדפן
- **ללא build** - קובץ HTML בודד
- **Hosting** - GitHub Pages

## הגדרת Google Cloud (חד פעמי)

האפליקציה משתמשת ב-Google Calendar API. כדי שהיא תעבוד צריך:

1. **פרויקט ב-Google Cloud** עם [Calendar API מופעל](https://console.cloud.google.com/apis/library/calendar-json.googleapis.com)
2. **OAuth Client ID** מסוג Web Application:
   - Authorized JavaScript origins: `https://shohamtal.github.io`
   - (לפיתוח מקומי גם: `http://localhost:PORT`)

ה-Client ID מוטמע ישירות בקוד - זה לא מידע רגיש, המשתמש עדיין חייב לאשר גישה דרך חלון ההתחברות של גוגל.

## פיתוח מקומי

```bash
# הפעלת שרת מקומי
python3 -m http.server 5199

# פתיחה בדפדפן
open http://localhost:5199
```

## טכנולוגיות

| רכיב | שימוש |
|------|-------|
| [`@hebcal/core`](https://github.com/hebcal/hebcal-es6) | חישובי לוח עברי |
| [Google Identity Services](https://developers.google.com/identity/gsi/web) | OAuth 2.0 בדפדפן |
| [Google Calendar API](https://developers.google.com/calendar/api) | יצירת אירועים |
| [GitHub Pages](https://pages.github.com/) | אירוח |
