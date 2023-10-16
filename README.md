#include <iostream>
#include <vector>
#include <fstream>
#include <string>
using namespace std;

void stringToVector(string str, vector<double>& vec) {
    string t{};
    for (int i = 0; i < str.length(); i++) {
        t += str[i];
        if (str[i] == ' ' || i + 1 == str.length()) {
            vec.push_back(stod(t));
            t = {};
        }
    }
}

int main() {
    setlocale(LC_ALL, "RUSSIAN");
    int N, IER = 0;
    vector<double> X, Y;
    ifstream f("file.txt");
    string str;
    getline(f, str);
    stringToVector(str, X);
    N = X.size();
    if (N < 3) {
        IER = 1;
        cout << "Ошибка: Кубический сплайн не может быть построен (N < 3)" << endl;
        return 0;
    }
    for (int i = 0; i < N - 1; i++) {
        if (X[i] > X[i + 1]) {
            IER = 2;
            cout << "Ошибка: нарушен порядок возрастания аргумента во входном векторе X" << endl;
            return 0;
        }
    }
    cout << "X: ";
    for (double x : X) {
        cout << x << "\t";
    }
    getline(f, str);
    stringToVector(str, Y);
    cout << "\nY: ";
    for (double y : Y) {
        cout << y << "\t";
    }
    double A, B;
    getline(f, str, ' ');
    A = stod(str);
    getline(f, str, ' ');
    B = stod(str);
    cout << "\n" << "A = " << A << " B = " << B << "\n";

    vector<double> a(N - 1), b(N - 1), c(N), d(N - 1), h(N - 1), F(N);

    for (int i = 1; i < N; i++) {
        a[i - 1] = Y[i];
        h[i - 1] = X[i] - X[i - 1];
    }
    F[0] = A, F[N - 1] = B;
    for (int i = 1; i < N - 1; i++) {
        F[i] = 6 * (((Y[i + 1] - Y[i]) / h[i]) - ((Y[i] - Y[i - 1]) / h[i - 1]));
    }

    vector <double> AA(N - 1), BB(N - 1), CC(N), M(N - 1), V(N - 1);

    AA[N - 2] = 0;
    for (int i = 0; i < N - 2; i++) {
        AA[i] = h[i];
    }

    BB[0] = 0;
    for (int i = 1; i < N - 1; i++) {
        BB[i] = h[i];
    }

    CC[0] = CC[N - 1] = 1;
    for (int i = 1; i < N - 1; i++) {
        CC[i] = 2 * (h[i - 1] + h[i]);
    }

    M[0] = (-1.0 * BB[0]) / CC[0];
    V[0] = F[0] / CC[0];

    for (int i = 1; i < N - 1; i++) {
        double t = CC[i] + M[i - 1] * AA[i - 1];
        M[i] = (-1.0 * BB[i]) / t;
        V[i] = (F[i] - AA[i - 1] * V[i - 1]) / t;
    }

    c[0] = A;
    c[N - 1] = (F[N - 1] - AA[N - 2] * V[N - 2]) / (CC[N - 1] + AA[N - 2] * M[N - 2]);

    for (int i = N - 2; i > 0; i--) {
        c[i] = M[i] * c[i + 1] + V[i];
    }
    
    for (int i = 0; i < N - 1; i++) {
        d[i] = (c[i + 1] - c[i]) / h[i];
    }

    for (int i = 0; i < N - 1; i++) {
        b[i] = (h[i] * c[i]) / 2.0 - (h[i] * h[i] * d[i]) / 6.0 + (Y[i + 1] - Y[i]) / h[i];
    }
    cout << "\nIER = " << IER;

    cout << "\n\n   \ta\tb\tc\td\t\n";
    for (int i = 0; i < N - 1; i++) {
        cout << "\nS" << i + 1 << "(x)" << "\t";
        cout << a[i] << "\t" << b[i] << "\t" << c[i + 1] << "\t" << d[i] << "\t";
    }
    /*cout << "\na: ";
    for (double val : a) {
        string t = to_string(val);
        cout << t << "\t";
    }
    cout << "\nb: ";
    for (double val : b) {
        string t = to_string(val);
        cout << t << "\t";
    }
    cout << "\nc: ";
    for (int i = 1; i < N; i++) {
        string t = to_string(c[i]);
        cout << t << "\t";
    }
    cout << "\nd: ";
    for (double val : d) {
        string t = to_string(val);
        cout << t << "\t";
    }*/

    return 0;
}
 //1 1,2 1,4 1,6 1,8 2
 //1 1,728 2,744 4,096 5,832 8
 //6 12
