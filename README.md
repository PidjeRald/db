#include <iostream>
#include <vector>
#include <fstream>
#include <string>
#include <algorithm>
#include <cmath>
#include <cstdlib>
#include <ctime>
#include <random>

using namespace std;

typedef double T;

bool F(int N, int k, vector<T> a, vector<T> b,
    vector<T> c, vector<T> p, vector<T> f, vector<T>& x) {

    for (int i = 0; i < k; i++) {
        if (b[i] == 0)
            return 1;
        T R = 1 / b[i];
        b[i] = 1;
        c[i] = c[i] * R;
        p[i] = p[i] * R;
        f[i] = f[i] * R;
        R = a[i];
        a[i] = 0;
        b[i + 1] = b[i + 1] - c[i] * R;
        p[i + 1] = p[i + 1] - p[i] * R;
        f[i + 1] = f[i + 1] - f[i] * R;
        if (i + 2 == k)
            c[i + 1] = p[i + 1];
    }

    for (int i = N - 2; i > k - 1; i--) {
        if (b[i + 1] == 0)
            return 1;
        T R = 1 / b[i + 1];
        b[i + 1] = 1;
        a[i] = a[i] * R;
        p[i + 1] = p[i + 1] * R;
        f[i + 1] = f[i + 1] * R;
        R = c[i];
        c[i] = 0;
        if (i == k)
            a[i] = p[i + 1];
        b[i] = b[i] - a[i] * R;
        p[i] = p[i] - p[i + 1] * R;
        f[i] = f[i] - f[i + 1] * R;
    }

    f[k] = f[k] / p[k];
    p[k] = b[k] = 1;
    for (int i = 0; i < N; i++)
        if (i != k) {
            f[i] = f[i] - f[k] * p[i];
            p[i] = 0;
        }

    x[k - 1] = f[k - 1];
    x[k] = f[k];
    x[k + 1] = f[k + 1];
    for (int i = k - 2; i > -1; i--)
        x[i] = f[i] - c[i] * x[i + 1];
    for (int i = k + 1; i < N - 1; i++)
        x[i + 1] = f[i + 1] - a[i] * x[i];
    reverse(x.begin(), x.end());
    return 0;
}

void F2(int N, int k, vector<T> a, vector<T> b,
    vector<T> c, vector<T> p, vector<T> &f, vector<T>x) {
    reverse(x.begin(), x.end());
    for (int i = 0; i < N; i++) {
        if (i == 0) {
            f[i] = p[i] * x[k] + b[i] * x[i] + c[i] * x[i + 1];
        }
        else if (i == N - 1) {
            f[i] = p[i] * x[k] + b[i] * x[i] + a[i - 1] * x[i - 1];
        }
        else if (i == k - 1) {
            f[i] = p[i] * x[k] + b[i] * x[i] + a[i - 1] * x[i - 1];
        }
        else if (i == k) {
            f[i] = p[i] * x[k] + c[i] * x[i + 1] + a[i - 1] * x[i - 1];
        }
        else if (i == k + 1) {
            f[i] = p[i] * x[k] + b[i] * x[i] + c[i] * x[i + 1];
        }
        else
            f[i] = p[i] * x[k] + b[i] * x[i] + c[i] * x[i + 1] + a[i - 1] * x[i - 1];
    }
    reverse(x.begin(), x.end());
}

int main() {
    setlocale(LC_ALL, "RUSSIAN");
    int N = 1000, k = 5, IER = 1, count = 0, h = 10;
    double min = h * -exp(1), max = h * exp(1);
    random_device rd;
    mt19937 gen(rd());
    uniform_real_distribution<double> dis(min, max);
    vector<double> c(N - 1), b(N), a(N - 1), p(N), xRand(N), xFromRand(N), xOnes(N), xFromOnes(N), fFromRand(N), fFromOnes(N);
    double dxOnes = 0, dxRand = 0, dxRandi, q = pow(10,-8), AVERAGEdxOnes = 0, AVERAGEdxRand = 0;
    for (int j = 0; j < 10; j++) {
        for (int i = 0; i < N; i++) {
            if (i + 1 != N) {
                c[i] = dis(gen);
                a[i] = dis(gen);
            }
            b[i] = dis(gen);
            p[i] = dis(gen);
            xRand[i] = dis(gen);
            xOnes[i] = 1;
        }
        p[k - 1] = c[k - 1];
        p[k] = b[k];
        p[k + 1] = a[k];

        F2(N, k, a, b, c, p, fFromRand, xRand);
        F2(N, k, a, b, c, p, fFromOnes, xOnes);

        IER = F(N, k, a, b, c, p, fFromRand, xFromRand);
        if (!IER) {
            F(N, k, a, b, c, p, fFromOnes, xFromOnes);
            count++;
            for (int i = 0; i < N; i++) {
                if (abs(xFromOnes[i] - 1.0) > dxOnes)
                    dxOnes = abs(xFromOnes[i] - 1);
            }
            AVERAGEdxOnes += dxOnes;
            for (int i = 0; i < N; i++) {
                if (xRand[i] > q)
                    dxRandi = abs((xFromRand[i] - xRand[i]) / xRand[i]);
                else
                    dxRandi = abs(xFromRand[i] - xRand[i]);
                if (dxRand < dxRandi)
                    dxRand = dxRandi;
            }
            AVERAGEdxRand += dxRand;
            dxOnes = dxRandi = dxRand = 0;
        }
    }
    cout << "Количество испытаний: " << count << "\nКоэффициент элементов матриц : " << h << "\nРазмерность системы : " << N;
    AVERAGEdxOnes /= count;
    AVERAGEdxRand /= count;
    cout << "\nСредняя относительная погрешность системы: " << AVERAGEdxRand << "\nСреднее значение оценки точности: " << AVERAGEdxOnes;
    //for (T t : c) {
    //    cout << t << " ";
    //}
    //cout << "\n";
    //for (T t : b) {
    //    cout << t << " ";
    //}
    //cout << "\n";
    //for (T t : a) {
    //    cout << t << " ";
    //}
    //cout << "\n";
    //for (T t : p) {
    //    cout << t << " ";
    //}
    //c{ 85.3,53.2,28.53,85.3,68.4,45.2,53.8,64.3,54.6 },
    //b{ 54.5,-34.93,5.63,0.2,5.38,16.7,35.8,94.59,38.5,89.3 },
    //a{ 76.3,76.34,32.5,43.64,36.65,58.4,75.8,24.6,85.2 },
    //p{ 49.6,75.32,76.4,28.3,68.4,16.7,58.4,75.3,53.3,76.43 },
    //x{ 67.02,23.22,13.65,15.32,16.95,24.55,12.34,35.64,75.8,89.38 },
    //f(N);
    /*f{ 12177.67,7344.722,7634.2654,3734.568,1829.9766,1875.2865,2272.706,5221.7905,5792.487,9258.7185 };*/
    /*c{1,1,1,2,1,2},
    b{ 1,2,1,2,3,3,1 },
    a{ 1,2,1,2,1,1 },
    p{ 2,1,1,2,2,2,1 },
    f{ 4,5,4,5,6,8,3 },*/
    /*c{5 ,2 ,1,3 ,4,1},
    b{ 3 ,4 ,2,5 ,1,2,1 },
    a{ 5 ,4 ,3,2 ,1,3 },
    p{ 2 ,1 ,1,5 ,2,4,3 },
    f{ 10,12,7,11,7,8,7 },*/
    /*c{ 6 ,5 ,4,2 ,3,6,6,1,7 },
    b{ 6 ,5 ,5,2 ,7,4,6,7,8,4 },
    a{ 8 ,5 ,3,8 ,6,5,4,3,1 },
    p{ 8 ,6,3,4,3,4,5,7,5,4 },
    f{ 5,3,1,3,5,7,9,5,3,2 },*/
    /*c{ 9,5,4,7,7,2 },
    b{ 7,6,5,4,5,5,4 },
    a{ 5,8,3,5,8,6 },
    p{ 5,4,7,7,5,8,9 },
    f{ 94,73,72,46,56,71,95 },*/
    /*c{7245,6542,7134,543,5434,8675,435,6543,2344},
    b{ 7526,6542,6524,5432,7453,1348,6432,4568,9786,5432 },
    a{ 2845,524,7654,7354,8765,6544,8765,6534,765 },
    p{ 6823,9873,2365,8753,5434,1348,6544,765,5437,6863 },
    f{ 161562101,161058792,84046985,125632028,153374625,162098043,107402212,143486871,144718659,87873807 },*/
    /*c{76,65,78,65,43,32,76,87,21},
    b{ 32,43,56,76,32,54,76,87,23,12 },
    a{ 23,54,34,21,54,23,54,43,32 },
    p{ 12,64,75,84,43,54,23,65,33,47 },
    f{ 5312,11362,17410,17695,6646,7882,10550,21674,9113,7122 },*/
    /*c{85.3,53.2,28.53,85.3,68.4,45.2,53.8,64.3,54.6},
    b{ 54.5,-34.93,5.63,0.2,5.38,16.7,35.8,94.59,38.5,89.3 },
    a{ 76.3,76.34,32.5,43.64,36.65,58.4,75.8,24.6,85.2 },
    p{ 49.6,75.32,76.4,28.3,68.4,16.7,58.4,75.3,53.3,76.43 },
    f{ 12177.67,7344.722,7634.2654,3734.568,1829.9766,1875.2865,2272.706,5221.7905,5792.487,9258.7185 },*/
    return 0;
}
//10 (exp, 10*exp, 100*exp)
//100 (exp, 10*exp, 100*exp)
//1000 (exp, 10*exp, 100*exp)
