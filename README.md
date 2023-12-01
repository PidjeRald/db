#include <iostream>
#include <cmath>
#include <fstream>
#include <string>
using namespace std;


double f(double x) {
    return x;
}
// IER = 0: 1           -1,5 2,5 0,01 1 0,01
// IER = 0: x           -1,4 2,1 0,01 1 0,01
// 
// IER = 0: x * x       -1 2 0,001 1 0,01
// IER = 0: x * x * x    0 1 0,01 1 0,01
// 
// IER = 1: pow(exp(1), x * x * x)      2,71 3,14 0,000001 1000 0,000000001
// 
// IER = 2: x * x      0 1 0,00001 4 0,01
// 
// IER = 3: x          1 0 0,01 1 0,01

double trapezoidFormula(double a, double b, int N) {
    double h = (b - a) / N;
    double sum = 0.5 * (f(a) + f(b));
    for (int i = 1; i < N; i++) {
        sum += f(a + i * h);
    }
    return h * sum;
}


int main() {
    setlocale(LC_ALL, "RUSSIAN");
    double a, b, eps, hMin;
    int N;
    ifstream f("file.txt"); 
    string str;
    getline(f, str, ' ');
    a = stod(str);
    getline(f, str, ' ');
    b = stod(str);
    getline(f, str, ' ');
    eps = stod(str);
    getline(f, str, ' ');
    N = stoi(str);
    getline(f, str, ' ');
    hMin = stod(str);
    cout << "Пределы интегрирования: " << a << ", " << b << "\nТочность: " << eps
        << "\nНачальное число частичных отрезков: " << N
        << "\nНаименьшее допустимое значение шага интегрирования: " << hMin << "\n\n";

    ofstream f2("file2.txt", ofstream::trunc);
    if (b <= a) {
        cout << "IER = 3" << endl;
        f2 << "IER = 3";
        return 0;
    }

    double I_h = trapezoidFormula(a, b, N);
    double I_h_half = trapezoidFormula(a, b, 2 * N);

    double oldError, error = (4.0 / 3.0) * abs(I_h - I_h_half);
    oldError = error + 1;

    while (error > eps && (b - a) / (2 * N) >= hMin && error < oldError) {
        N *= 2;
        I_h = I_h_half;
        I_h_half = trapezoidFormula(a, b, 2 * N);
        oldError = error;
        error = (4.0 / 3.0) * abs(I_h - I_h_half);
    }

    if (error <= eps) {
        str = "IER = 0\nI(h) = " + to_string(I_h_half) + "\nEps = " + to_string(error) 
            + "\nH = " + to_string((b - a) / N) + "\n";
        cout << "IER = 0" << endl;
        cout << "I(h) = " << I_h_half << endl;
        cout << "Eps = " << error << endl;
        cout << "H = " << (b - a) / N << endl;
        f2 << str;
    }

    else if ((b - a) / (2 * N) < hMin) {
        cout << "IER = 2" << endl;
        f2 << "IER = 2";
    }

    else if (error >= oldError) {
        cout << "IER = 1" << endl;
        f2 << "IER  = 1";
    }

    return 0;
}
