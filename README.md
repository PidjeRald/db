#include <iostream>
#include <cmath>
#include <fstream>
#include <string>
using namespace std;


double f(double x) {
    return exp(1) * x;
}


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
    int N, H = 1;
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

    if (b <= a) {
        cout << "IER = 3" << endl;
        return 0;
    }

    double I_h = trapezoidFormula(a, b, N);
    double I_h_half = trapezoidFormula(a, b, 2 * N);

    double error = (4.0 / 3.0) * abs(I_h - I_h_half), oldError = -1;

    while (error >= eps && (b - a) / (2 * N) >= hMin && error != oldError ) {
        N *= 2;
        I_h = I_h_half;
        I_h_half = trapezoidFormula(a, b, 2 * N);
        oldError = error;
        error = (4.0 / 3.0) * abs(I_h - I_h_half);
        H++;
    }

    if (error < eps) {
        cout << "IER = 0" << endl;
        cout << "I(h) = " << I_h_half << endl;
        cout << "Eps = " << error << endl;
        cout << "H = " << H << endl;
    }
    else if ((b - a) / (2 * N) < hMin) {
        cout << "IER = 2" << endl;
    }
    else {
        cout << "IER = 1" << endl;
    }

    return 0;
}
// IER = 0: -1 2 0.000000001 4 0.00000001
// IER = 1: 0 1 0.00001 4 0.01
// IER = 2: 0 1 0.00001 4 0.01
