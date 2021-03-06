---
utid: 1000-02-13
chapter: 02
chaptername: فصل دوم - حوزهٔ مکان
part: 13
title: برابرسازی بافت‌نگار
_index: equalize-histogram
---

همانطور که در بخش‌های قبل توضیح دادیم، بافت‌نگار تصویر یک نمایش گرافیکی از توزیع روشنایی آن تصویر است. این نمودار تعداد پیکسل‌هایی که یک مقدار روشنایی خاص را دارند، تعیین می‌کند.

| ![یک تصویر سیاه و سفید و بافت‌نگارآن](/opencv-book/media/image71.png) |
| :----------------------------------------------------------: |
|              یک تصویر سیاه و سفید و بافت‌نگار آن              |

برابرسازی بافت‌نگار روشی برای بهبود کنتراست تصویر است. این کار از طریق وسیع کردن برد روشنایی انجام می‌شود.

در شکل بالا می‌بینید که بیشتر پیکسل‌ها در دسته‌های میانی برد روشنایی قرار گرفته‌اند. کاری که برابرسازی بافت‌نگار انجام می‌دهد، وسیع کردن این برد است. به شکل زیر توجه کنید. در این شکل دایره‌های سبز رنگ نشان دهندهٔ نواحی کم پوشش هستند (یعنی پیکسل‌های کمتری دارای این مقادیر از روشنایی هستند). بعد از اعمال برابرسازی، بافت‌نگاری شبیه شکل میانی به دست می‌آید. تصویر سمت راست نتیجهٔ این برابرسازی است.

| ![فرایند برابرسازی بافت‌نگار یک تصویر – تصویر سمت راست برابرسازی است](/opencv-book/media/image72.png) |
| :----------------------------------------------------------: |
| فرایند برابرسازی بافت‌نگار یک تصویر – تصویر سمت راست برابرسازی است |

عملیات برابرسازی، یک توزیع را به یک توزیع وسیع‌تر و یکنواخت‌تر نگاشت می‌کند. پس مقادیر روشنایی، روی همهٔ بُرد موجود توزیع می‌شوند.

برای رسیدن به اهداف برابرسازی، تابع مورد استفاده برای برابرسازی باید از نوع تابع توزیع تجمعی[^a] (CDF) باشد. برای بافت‌نگار$H(i)$، توزیع تجمعی آن، یعنی $H'(i)$، به صورت زیر است:

$$H'\left( i \right) = \sum_{0 \leq j < i}^{}{H(j)}$$

برای استفاده از این تابع به عنوان تابع نگاشت باید $H'(i)$ را به گونه‌ای نرمال سازی کنیم که مقادیر آن بین 0 تا 255 قرار بگیرند. برای مثال بالا، تابع تجمعی به صورت زیر می‌شود:

| ![تابع توزیع تجمعی بافت‌نگار تصویر سگ](/opencv-book/media/image73.png) |
| :----------------------------------------------------------: |
|              تابع توزیع تجمعی بافت‌نگار تصویر سگ              |

در انتها برای به دست آوردن مقادیر روشنایی تصویر برابرسازی شده، از یک نگاشت ساده استفاده می‌کنیم:

$$\text{equalized}\left( x,\ y \right) = H'\left( \text{src}\left( x,\ y \right) \right)$$

[^a]: Cumulative Distribution Function (CDF)



## کد

```c++
#include "opencv2/highgui/highgui.hpp"
#include "opencv2/imgproc/imgproc.hpp"
#include <iostream>
#include <stdio.h>

using namespace cv;
using namespace std;

/**  @function main */
int main( int argc, char** argv )
{
  Mat src, dst;

  char* source_window = "Source image";
  char* equalized_window = "Equalized Image";

  /// Load image
  src = imread( argv[1], 1 );

  if( !src.data )
    { cout<<"Usage: ./Histogram_Demo <path_to_image>"<<endl;
      return -1;}

  /// Convert to grayscale
  cvtColor( src, src, CV_BGR2GRAY );

  /// Apply Histogram Equalization
  equalizeHist( src, dst );

  /// Display results
  namedWindow( source_window, CV_WINDOW_AUTOSIZE );
  namedWindow( equalized_window, CV_WINDOW_AUTOSIZE );

  imshow( source_window, src );
  imshow( equalized_window, dst );

  /// Wait until user exits the program
  waitKey(0);

  return 0;
}
```



## توضیح

در خط 25 تصویر ورودی را به یک تصویر سیاه و سفید تبدیل می‌کنیم.

در خط 28 با استفاده از تابع equalizeHist، عملیات برابرسازی بافت‌نگار را انجام می‌دهیم. این تابع دو آرگومان دارد که اولی تصویر ورودی و دومی تصویر خروجی است.



## خروجی

از تصویر زیر که کنتراست کمی دارد به عنوان تصویر ورودی استفاده می‌کنیم:

| ![تصویر ورودی](/opencv-book/media/image74.png) | ![بافت‌نگار تصویر ورودی](/opencv-book/media/image75.png) |
| :--------------------------------------------: | :-----------------------------------------------------: |
|                  تصویر ورودی                   |                  بافت‌نگار تصویر ورودی                   |

بعد از اعمال برابرسازی نتیجه به صورت زیر می‌شود:

| ![تصویر خروجی](/opencv-book/media/image76.png) | ![بافت‌نگار تصویر خروجی](/opencv-book/media/image77.png) |
| :--------------------------------------------: | :-----------------------------------------------------: |
|                  تصویر خروجی                   |                  بافت‌نگار تصویر خروجی                   |

