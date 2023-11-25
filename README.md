using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Collections.Specialized;
using System.Drawing;
using System.Drawing.Drawing2D;
using System.Drawing.Imaging;


class ImageProcessor
{
    private Bitmap image;

    public ImageProcessor(string imagePath)
    {
        image = new Bitmap(imagePath);
    }

    public void SaveImage(string outputPath)
    {
        image.Save(outputPath);
    }

    public void NearestNeighbor(int newWidth, int newHeight)
    {
        Bitmap resizedImage = new Bitmap(newWidth, newHeight);
        float widthRatio = (float)image.Width / newWidth;
        float heightRatio = (float)image.Height / newHeight;
        for (int x = 0; x < newWidth; x++)
        {
            for (int y = 0; y < newHeight; y++)
            {
                int originalX = (int)(x * widthRatio);
                int originalY = (int)(y * heightRatio);
                resizedImage.SetPixel(x, y, image.GetPixel(originalX, originalY));
            }
        }
        image = resizedImage;
    }

    public void BicubicInterpolation(int newWidth, int newHeight)
    {
        Bitmap resizedImage = new Bitmap(newWidth, newHeight);
        float widthRatio = (float)image.Width / newWidth;
        float heightRatio = (float)image.Height / newHeight;
        for (int x = 0; x < newWidth; x++)
        {
            for (int y = 0; y < newHeight; y++)
            {
                float originalX = x * widthRatio;
                float originalY = y * heightRatio;
                Color color = BicubicInterpolate(originalX, originalY);
                resizedImage.SetPixel(x, y, color);
            }
        }
        image = resizedImage;
    }

    // Метод вычисляет цвет с помощью бикубической интерполяции на основе окружающих пикселей и коэффициентов
    private Color BicubicInterpolate(float originalX, float originalY)
    {
        // Определяем индексы ближайших пикселей
        int left = (int)Math.Floor(originalX) - 1;
        int top = (int)Math.Floor(originalY) - 1;
        // Вычисляем коэффициенты для интерполяции по X и Y
        float u = originalX - left;
        float v = originalY - top;

        float[] coefficientsX = GetBicubicCoefficients(u);
        float[] coefficientsY = GetBicubicCoefficients(v);

        Color color = Color.FromArgb(0, 0, 0);
        // Проходим по каждому пикселю в окрестности
        for (int i = 0; i < 4; i++)
        {
            for (int j = 0; j < 4; j++)
            {
                // Определяем индексы текущего пикселя
                int x = left + i;
                int y = top + j;
                // Если пиксель находится в пределах изображения
                if (x >= 0 && x < image.Width && y >= 0 && y < image.Height)
                {
                    // Получаем цвет текущего пикселя
                    Color pixelColor = image.GetPixel(x, y);
                    // Вычисляем коэффициент для текущего пикселя
                    float coefficient = coefficientsX[i] * coefficientsY[j];
                    // Вычисляем новые значения цветовых компонент и ограничиваем их в диапазоне от 0 до 255
                    int red = (int)(pixelColor.R * coefficient);
                    int green = (int)(pixelColor.G * coefficient);
                    int blue = (int)(pixelColor.B * coefficient);

                    red = Math.Max(0, Math.Min(255, red));
                    green = Math.Max(0, Math.Min(255, green));
                    blue = Math.Max(0, Math.Min(255, blue));
                    // Создаем новый цвет на основе полученных значений
                    color = Color.FromArgb(red, green, blue);
                }
            }
        }
        // Возвращаем полученный цвет
        return color;
    }

    // Метод вычисляет коэффициенты для бикубической интерполяции по заданному параметру t
    private float[] GetBicubicCoefficients(float t)
    {
        // Создаем массив для хранения коэффициентов
        float[] coefficients = new float[4];
        // Вычисляем значения для t^2 и t^3
        float t2 = t * t;
        float t3 = t2 * t;

        // Вычисляем коэффициенты по формуле
        coefficients[0] = ((-0.5f * t3) + t2 - (0.5f * t)) + 1;
        coefficients[1] = ((1.5f * t3) - (2.5f * t2) + 1);
        coefficients[2] = ((-1.5f * t3) + (2.0f * t2) + (0.5f * t));
        coefficients[3] = ((0.5f * t3) - (0.5f * t2));

        // Возвращаем массив коэффициентов
        return coefficients;
    }




    private int[] GetHistogram(Bitmap image)
    {
        int[] histogram = new int[256];
        for (int y = 0; y < image.Height; y++)
        {
            for (int x = 0; x < image.Width; x++)
            {
                Color pixel = image.GetPixel(x, y);
                int intensity = (int)(0.299 * pixel.R + 0.587 * pixel.G + 0.114 * pixel.B);
                histogram[intensity]++;
            }
        }
        return histogram;
    }

    private int[] GetCumulativeHistogram(int[] histogram)
    {
        int[] cumulativeHistogram = new int[256];
        cumulativeHistogram[0] = histogram[0];
        for (int i = 1; i < 256; i++)
        {
            cumulativeHistogram[i] = cumulativeHistogram[i - 1] + histogram[i];
        }
        return cumulativeHistogram;
    }

    public void HistogramEqualization()
    {
        int[] histogram = GetHistogram(image);
        int[] cumulativeHistogram = GetCumulativeHistogram(histogram);
        for (int y = 0; y < image.Height; y++)
        {
            for (int x = 0; x < image.Width; x++)
            {
                Color pixel = image.GetPixel(x, y);
                int intensity = (int)(0.299 * pixel.R + 0.587 * pixel.G + 0.114 * pixel.B);
                int newIntensity = cumulativeHistogram[intensity] * 255 / (image.Width * image.Height);
                Color newPixel = Color.FromArgb(newIntensity, newIntensity, newIntensity);
                image.SetPixel(x, y, newPixel);
            }
        }
    }

    public void HistogramStretching()
    {
        int minIntensity = 255;
        int maxIntensity = 0;
        for (int y = 0; y < image.Height; y++)
        {
            for (int x = 0; x < image.Width; x++)
            {
                Color pixel = image.GetPixel(x, y);
                int intensity = (int)(0.299 * pixel.R + 0.587 * pixel.G + 0.114 * pixel.B);
                if (intensity < minIntensity)
                {
                    minIntensity = intensity;
                }
                if (intensity > maxIntensity)
                {
                    maxIntensity = intensity;
                }
            }
        }
        for (int y = 0; y < image.Height; y++)
        {
            for (int x = 0; x < image.Width; x++)
            {
                Color pixel = image.GetPixel(x, y);
                int intensity = (int)(0.299 * pixel.R + 0.587 * pixel.G + 0.114 * pixel.B);
                int newIntensity = (int)((intensity - minIntensity) * 255.0 / (maxIntensity - minIntensity));
                Color newPixel = Color.FromArgb(newIntensity, newIntensity, newIntensity);
                image.SetPixel(x, y, newPixel);
            }
        }
    }
}



class Program
{
    static void Main()
    {
        string imagePath = "C:\\Users\\asdf\\Desktop\\ilya.png";
        string outputImagePath1 = "C:\\Users\\asdf\\Desktop\\2.png";
        string outputImagePath2 = "C:\\Users\\asdf\\Desktop\\3.png";

        ImageProcessor image1 = new ImageProcessor(imagePath);
        image1.NearestNeighbor(1000, 850);
        image1.SaveImage(outputImagePath1);

        ImageProcessor image2 = new ImageProcessor(imagePath);
        image2.BicubicInterpolation(1000, 850);
        image2.SaveImage(outputImagePath2);

    }
}
