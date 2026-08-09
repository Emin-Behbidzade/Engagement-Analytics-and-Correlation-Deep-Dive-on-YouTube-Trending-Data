# Engagement-Analytics-and-Correlation-Deep-Dive-on-YouTube-Trending-Data
# YouTube Trending (US) — Engagement Analytics Metodologiya və Nəticələr

## Metodologiya

- `USvideos.csv` yükləndi (40 949 sətir, 16 sütun), `US_category_id.json` parse edilib `category_name` sütunu əlavə edildi (16 unikal kateqoriya, hamısı uğurla map olundu)
- `trending_date` xüsusi `YY.DD.MM` formatındadır (məs. `17.14.11` = 2017-11-14); `publish_time` ISO 8601 + UTC timezone-dur, müqayisə üçün timezone atıldı
- Metriklər hesablandı:
  - `engagement_rate` = (likes + dislikes + comment_count) / views × 100
  - `like_rate` = likes / views × 100
  - `days_to_trend` = trending_date − publish tarixi (gün)
- Dublikatlar silindi: hər video üçün yalnız ilk trend olduğu tarix saxlanıldı (40 949 → **6 351 unikal video**; orta hesabla hər video 6.4 gün trend siyahısında qalıb)
- Data keyfiyyəti qeydi: 87 video (1.4%) `days_to_trend` > 60 gün göstərir (köhnə reklamlar/klipin yenidən populyarlaşması) , bunlar silinmədi, lakin ortalamalara təsirini azaltmaq üçün median dəyərlər də hesablandı

## Əsas Tapıntılar

**Korrelyasiya matrisi (unikal videolar üzrə):**
- views ↔ likes: r = 0.76 (güclü müsbət)
- likes ↔ comment_count: r = 0.81 (ən güclü əlaqə)
- dislikes ↔ comment_count: r = 0.68 (mübahisəli videolar daha çox şərh alır)
- views ↔ engagement_rate: r = 0.06 (demək olar heç əlaqə yoxdur!)
- days_to_trend ↔ engagement_rate: r = −0.10 (zəif mənfi)

**Kateqoriya üzrə:**
- Ən yüksək orta views: **Music** (1.39M) və **Gaming** (1.21M)
- Ən yüksək orta engagement rate: **Music** (8.23%), sonra **Howto & Style** (5.97%) və **Comedy** (5.69%)
- Ən aşağı engagement rate: **Sports** (2.04%) və **Autos & Vehicles** (2.25%) — yüksək views-a baxmayaraq (Sports 830K orta views)

**Həftənin günü üzrə:**
- Ən yüksək orta views: **Friday** (894K) və **Sunday** (859K)
- One-way ANOVA: F = 1.63, p = 0.134 — fərq **statistik cəhətdən əhəmiyyətli deyil** (həftənin günü tək başına views-un güclü proqnozlaşdırıcısı deyil)

**Top 10 trend video (views üzrə):** demək olar hamısı **Music** və **Entertainment** kateqoriyasındadır, 1-7 gün ərzində trend olub (məs. BTS "FAKE LOVE" — 39.3M views, cəmi 1 gündə trend oldu)

## 5 Content Strategy Insight-i

1. **Views və engagement_rate demək olar müstəqildir (r = 0.06).** Çox baxılan video mütləq yüksək faiz nisbətində bəyənilmə/şərh almır, o deməkdir ki, "reach" (əhatə) və "engagement" (bağlılıq) ayrı-ayrı optimallaşdırılmalı metriklərdir. Yalnız views-a fokuslanan strategiya loyal auditoriya yaratmır.

2. **Comment_count ən güclü "iştirak" siqnalıdır, lakin mübahisə ilə bağlıdır.** Comment_count həm likes (r=0.81), həm də dislikes (r=0.68) ilə güclü əlaqəlidir — yəni şərh sayının yüksək olması təkcə müsbət deyil, həm də mübahisəli/polarizasiya edən məzmunun əlaməti ola bilər. Şərh sayını tək uğur metriki kimi istifadə etmək yanlışdır, sentiment də nəzərə alınmalıdır.

3. **Music kateqoriyası həm reach, həm engagement baxımından liderdir** (1.39M orta views, 8.23% engagement rate  hər iki metrikdə #1). Digər kateqoriyalar arasında güclü ziddiyyət var: Sports yüksək views (830K) götürür, lakin ən aşağı engagement-ə (2.04%) malikdir, bu, passiv izləyici auditoriyasını göstərir. Content strategiyası quran komanda üçün: Music-format elementləri (musiqi, vizual enerji) digər kateqoriyalara inteqrasiya etmək engagement-i artıra bilər.

4. **Nəşr günü tək başına views-u əhəmiyyətli dərəcədə dəyişmir (ANOVA p=0.13).** Cümə və Bazar günləri orta hesabla bir qədər yüksəkdir, lakin fərq statistik cəhətdən təsadüfi ola bilər. Content komandaları nəşr vaxtını yeganə lever kimi görməməli məzmun keyfiyyəti və kateqoriya seçimi daha güclü faktorlardır.

5. **Trend olma sürəti çox yüksəkdir (median 2 gün) və engagement ilə zəif mənfi əlaqəlidir (r=-0.10).** Trend siyahısına tez düşən videolar bir qədər daha yüksək engagement rate göstərir, "ilk 48 saat" strategiyasının (nəşrdən sonrakı ilk gündə paylaşım/təşviqat) əhəmiyyətini vurğulayır. Videonun ilk günlərdəki performansı alqoritmik təşviq üçün kritikdir.

- `visuals/views_by_publish_weekday.png` — həftənin günü üzrə views paylanması
- `visuals/views_vs_engagement_scatter.png` — views vs engagement_rate (bonus scatter)
- `visuals/category_kpi.csv`, `weekday_kpi.csv`, `correlation_matrix.csv`, `top10_videos_by_views.csv` — tam KPI cədvəlləri
