https://sun9-65.userapi.com/impg/JNpbXtp5N8JqmGNJ6fF12sWUvkXinpOwewgmWw/gb0hCC-SBhw.jpg?size=1620x2160&quality=95&sign=50ee3d5781f1f62b4b407841ba8a32db&type=album
https://sun9-79.userapi.com/impg/7vDYI7_ryfGd57pIaUrZKGompkVIQLUdyK5NiA/o6kfmImSGso.jpg?size=1620x2160&quality=95&sign=98665ebe7580cf0192668e63c5be89e9&type=album


using System;
using MathNet.Numerics.LinearAlgebra;

class Program
{
    static void Main()
    {
        double eps = 0.2;
        double x0 = -1;
        double y0 = -1;
        double[] result1 = minim_method(x0, y0, 10, eps);
        Console.WriteLine($"x_10 = {result1[0]}, {result1[1]}, f(x_10) = {f(result1[0], result1[1])}");

        double[] result2 = minim_method(x0, y0, 100, eps);
        Console.WriteLine($"x_100 = {result2[0]}, {result2[1]}, f(x_100) = {f(result2[0], result2[1])}");

        double[] result3 = minim_method(x0, y0, 10000, eps);
        Console.WriteLine($"x_1000 = {result3[0]}, {result3[1]}, f(x_1000) = {f(result3[0], result3[1])}");
    }

    static double f(double u1, double u2)
    {
        return u1 * u1 + u1 * u2 + u2 * u2;
    }

    static double g_1(double u1, double u2)
    {
        return u1 + u2 - 2;
    }

    static double P_k(double u1, double u2, double k)
    {
        return k * Math.Pow(g_1(u1, u2), 2);
    }

    static double F_k(double u1, double u2, double k)
    {
        return f(u1, u2) + P_k(u1, u2, k);
    }

    static double dFx(double x, double y, double k)
    {
        return (F_k(x + 0.0001, y, k) - F_k(x, y, k)) / 0.0001;
    }

    static double dFy(double x, double y, double k)
    {
        return (F_k(x, y + 0.0001, k) - F_k(x, y, k)) / 0.0001;
    }

    static double ddFx(double x, double y, double k)
    {
        return (dFx(x + 0.0001, y, k) - dFx(x, y, k)) / 0.0001;
    }

    static double ddFy(double x, double y, double k)
    {
        return (dFy(x, y + 0.0001, k) - dFy(x, y, k)) / 0.0001;
    }

    static double ddFxy(double x, double y, double k)
    {
        return (dFy(x + 0.0001, y, k) - dFy(x, y, k)) / 0.0001;
    }

    static double findNormOfgrad(double x, double y, double k)
    {
        return Math.Sqrt(Math.Pow(dFx(x, y, k), 2) + Math.Pow(dFy(x, y, k), 2));
    }

    static double findgradX(double x, double y, double k)
    {
        return dFx(x, y, k);
    }

    static double findgradY(double x, double y, double k)
    {
        return dFy(x, y, k);
    }

    static Matrix<double> findH(double x0, double y0, double k)
    {
        double[,] hArray = { { ddFx(x0, y0, k), ddFxy(x0, y0, k) }, { ddFxy(x0, y0, k), ddFy(x0, y0, k) } };
        return Matrix<double>.Build.DenseOfArray(hArray);
    }

    static double[] minim_method(double x0, double y0, double k, double eps)
    {
        double f0 = F_k(x0, y0, k);
        Matrix<double> H = findH(x0, y0, k).Inverse();
        while (findNormOfgrad(x0, y0, k) >= eps)
        {
            var gradX = findgradX(x0, y0, k);
            var gradY = findgradY(x0, y0, k);
            var updatedX = x0 - (gradX * H[0, 0] + gradY * H[1, 0]);
            var updatedY = y0 - (gradX * H[0, 1] + gradY * H[1, 1]);
            x0 = updatedX;
            y0 = updatedY;
        }

        return new double[] { x0, y0 };
    }
}
