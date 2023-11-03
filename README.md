# OpenCVSharp
## Tạo hình tròn kích thước 5pixel
    Cv2.Circle(img, new Point(0, img.Rows - 400), 5, new Scalar(255, 0, 0), 5);    
## Vẽ khung theo các điểm point
    Cv2.Polylines(img, new List<Point[]> { selectionPoints }, true, new Scalar(255, 0, 0), 3);    
## Chuyển đổi hình ảnh từ không gian màu BGR sang HSV.
    Mat hsvImage = img.CvtColor(ColorConversionCodes.BGR2HSV);
## Tạo mặt nạ cho các đối tượng màu cam.
    Scalar lowerOrange = new Scalar(0, 100, 100);
    Scalar upperOrange = new Scalar(30, 255, 255);
    Mat orangeMask = new Mat();
    Cv2.InRange(hsvImage, lowerOrange, upperOrange, orangeMask);
### Tạo mặt nạ cho vùng quan tâm.
    Mat regionOfInterestMask = new Mat(img.Size(), MatType.CV_8UC1, Scalar.White);
    Cv2.FillPoly(regionOfInterestMask, new List<Point[]> { selectionPoints }, Scalar.White);
### Kết hợp hai mặt nạ lại để tạo mặt nạ cuối cùng.
    Mat combinedMask = new Mat();
    Cv2.BitwiseAnd(orangeMask, regionOfInterestMask, combinedMask);
### Sao chép hình ảnh img vào copy và che chắn bởi mặt nạ cuối cùng
    copy = new Mat(img.Size(), MatType.CV_8UC3);
    Cv2.BitwiseAnd(img, img, copy, combinedMask);
## Làm mịn hình ảnh
    Cv2.GaussianBlur(copy, copy, new Size(9, 9), 0);
## Tách nền khỏi hình ảnh
    BackgroundSubtractorMOG2 subtrac = BackgroundSubtractorMOG2.Create();
    subtrac.Apply(copy, sub);
## Áp dụng bộ lọc mở để loại bỏ nhiễu.
    Mat ker = Cv2.GetStructuringElement(MorphShapes.Ellipse, new Size(5, 5));
    Cv2.MorphologyEx(sub, sub, MorphTypes.Close, ker, iterations: 2);
## Lấy các vùng theo ngưỡng nhị phân
     Cv2.Threshold(sub, sub, 200, 255, ThresholdTypes.Binary);
## Tìm các đường bao của các đối tượng trong hình ảnh
    Point[][] contours;
    HierarchyIndex[] hierarchy;
    Cv2.FindContours(sub, out contours, out hierarchy, RetrievalModes.List, ContourApproximationModes.ApproxSimple);
## Tính diện tích của đói tượng contours
     double area = Cv2.ContourArea(contour);
## Vẽ các đường bao quanh các đối tượng được tìm thấy.
    Rect rect = Cv2.BoundingRect(contour);
    img.Rectangle(rect, new Scalar(0, 255, 0), 2);
    
