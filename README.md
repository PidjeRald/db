#include <iostream>
#include <vector>
using namespace std;

double f(vector<double> x) {
    return 2 * x[0] * x[0] + 5 * x[1] * x[1] + 19;
}

vector<double> grad_f(vector<double> x) {
    vector<double> grad(x.size());
    grad[0] = 4 * x[0];
    grad[1] = 10 * x[1];
    return grad;
}

double norma_grad_f(vector<double> x) {
    double value = 0;
    for (int i = 0; i < x.size(); ++i) {
        value += pow(grad_f(x)[i], 2);
    }
    value = pow(value, 0.5);
    return value;
}

vector<double> gradientMethod(double epsilon, double alpha, vector<double> x0) {
    vector<double> x = x0;
    double f_x = f(x);

    while (true) {

        if (norma_grad_f(x) < epsilon) {
            break;
        }

        vector<double> new_x(x.size());
        for (int i = 0; i < x.size(); i++) {
            new_x[i] = x[i] - alpha * grad_f(x)[i];
        }
        double new_f_x = f(new_x);

        if (new_f_x < f_x) {
            x = new_x;
            f_x = new_f_x;
        }

        else {
            alpha = alpha / 2;
        }
    }

    return x;
}

int main() {
    double h = 0.01, epsilon = 0.00001, a = 0.3, b = 0.1;
    vector<double> x = { a,b };
    vector<double> z = gradientMethod(h, epsilon, x);
    for (double xi : z)
        cout << xi << endl;
    cout << f(z);
    return 0;
}





=================================================================================================================================================
=================================================================================================================================================
=================================================================================================================================================



#include <iostream>
#include <vector>
using namespace std;

double f(vector<double> x) {
    return x[0] * x[0] + 2 * x[1] * x[1] - 4 * x[0] + 2 * x[1];
}

vector<double> grad_f(vector<double> x) {
    vector<double> grad(x.size());
    grad[0] = 2 * x[0] - 4;
    grad[1] = 4 * x[1] + 2;
    return grad;
}

vector<double> H_grad_f(vector<double> x) {
    vector<double> grad = grad_f(x);
    grad[0] *= 0.5;
    grad[1] *= 0.25;
    return grad;
}

double norma_grad_f(vector<double> x) {
    double value = 0;
    for (int i = 0; i < x.size(); i++) {
        value += pow(grad_f(x)[i], 2);
    }
    value = pow(value, 0.5);
    return value;
}


//double f(vector<double> x) {
//    return 2 * x[0] * x[0] + x[1] * x[1] - 12 * x[0];
//}
//
//vector<double> grad_f(vector<double> x) {
//    vector<double> grad(x.size());
//    grad[0] = 4 * x[0] - 12;
//    grad[1] = 2 * x[1];
//    return grad;
//}
//
//vector<double> H_grad_f(vector<double> x) {
//    vector<double> grad = grad_f(x);
//    grad[0] *= 0.25;
//    grad[1] *= 0.5;
//    return grad;
//}
//
//double norma_grad_f(vector<double> x) {
//    double value = 0;
//    for (int i = 0; i < x.size(); i++) {
//        value += pow(grad_f(x)[i], 2);
//    }
//    value = pow(value, 0.5);
//    return value;
//}

vector<double> gradientMethod(double epsilon, vector<double> x0) {
    vector<double> x = x0;
    double f_x = f(x);

    while (true) {
        double norm_grad = norma_grad_f(x);
        if (norm_grad < epsilon) {
            break;
        }

        vector<double> new_x(x.size());
        for (int i = 0; i < x.size(); i++) {
            new_x[i] = x[i] - H_grad_f(x)[i];
        }
        double new_f_x = f(new_x);

        x = new_x;
    }

    return x;
}

int main() {
    double epsilon = 0.01, a = 1, b = 0;
    vector<double> x = { a,b };
    vector<double> z = gradientMethod(epsilon, x);
    for (double xi : z)
        cout << xi << endl;
    cout << f(z);
    return 0;
}
