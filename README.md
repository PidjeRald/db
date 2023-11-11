using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Collections.Specialized;
using System.Drawing;

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
                Color interpolatedColor = BicubicInterpolate(originalX, originalY);
                resizedImage.SetPixel(x, y, interpolatedColor);
            }
        }
        image = resizedImage;
    }

    private Color BicubicInterpolate(float x, float y)
    {
        return Color.Black; 
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
