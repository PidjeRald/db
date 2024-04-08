https://sun9-24.userapi.com/impg/FBRsO070-3xEH6rEoBJF9ZDlyD9SBsV_EgryUQ/b1j1jRSXLA0.jpg?size=1620x2160&quality=95&sign=a71bff9cff634f879d752aadada5ce7b&type=album

https://sun9-46.userapi.com/impg/OhlqFAss3rW5wWU_8tVnLzP7ofHCeUUw8L25dQ/vQdFMcb_i9Q.jpg?size=1620x2160&quality=95&sign=0df6f48166bee3004b0ef365bcbd217a&type=album

https://sun9-38.userapi.com/impg/9q45SO8kWk6eFeHbDYWVbizjDUo-nMAYoZPqsQ/p57NVcW_484.jpg?size=1620x2160&quality=95&sign=4c8fed216bf0c20aa9a5793e8a69ca9b&type=album

https://sun9-9.userapi.com/impg/j87krfCRuMW1WTZgb5mSJjWdNXAOUe-yKEPkew/Jex8bcZjf2g.jpg?size=2560x1920&quality=95&sign=36e95cae08c05caf66e1fed5e7d33233&type=album

https://sun9-77.userapi.com/impg/frW2Lk9QFFDMNLCRN45L6epJRu039gVQyy7hgQ/k2BD_t0R9os.jpg?size=836x484&quality=96&sign=1780352b4ff47c515d224d4678302b9d&type=album

clear
clc

% 1. Ввод матрицы A и создание копии A1
A = zeros(3, 3);
for i = 1:3
  for j = 1:3
    A(i, j) = i + 2*j - 1;
  end
end
A1 = A

% 2. Матрицы ONE и Z
ONE = ones(3, 3)
Z = zeros(3, 3)

% 3. Единичная матрица E
E = eye(3)

% 4. Удаление строки и столбца
A2 = A(2:3, :)
A3 = A(:, 1:2)

% 5. Добавление столбца
a = [7; 8; 9];
A4 = [A a]

% 6. Транспонированная матрица B
B = A.'

% 7. Сложение, вычитание
A_plus_B = A + B;
A_minus_B = A - B;
A_plus_ONE = A + ONE;
A_minus_ONE = A - ONE;
B_plus_Z = B + Z;
B_minus_Z = B - Z;
B_plus_2A = B + 2*A;
B_na_3 = 3*B;

% 8. Поэлементные операции
A_umnoz_B = A .* B;
A_delit_B = A ./ B;
A_quadrat = A.^2;

% 9. Умножение матриц
A_umnoz_B = A * B;
B_umnoz_E = B * E;
A_umnoz_E = A * E;
A_umnoz_ONE = A * ONE;

% 10. Векторы-строки
a = A(1, :)
b = A(3, :)
c = A(:, 2)
d = A(:, 3)

% 11. Перестановка строк и столбцов
A = [A(2, :); A(1, :); A(3, :)]
A = [A(:, 3) A(:, 1) A(:, 2)]

% 12. Диагональ и след
d = diag(A)
sled_A = sum(d);

% 13. Определители
det_A = det(A);
det_B = det(B);
det_E = det(E);
det_ONE = det(ONE);

% 14. Ранг матрицы
rank_A = rank(A);

% 15. Изменение элемента A(2,3)
A(2, 3) = 27

% 16. Обратная матрица AI
AI = inv(A)

% 17. AI*A
AI_na_A = AI * A

% 18. Восстановление A
A = inv(AI)

%%%%%%%%%%%%%%%%%%%%%%%%%%

% 1. Найти аргумент комплексного числа z=(1+9i)(2-3i)
z = (1 + 9i) * (2 - 3i);
arg_z = angle(z);
disp(['Аргумент комплексного числа z = ', num2str(arg_z)]);

% 2. Найти первообразную функции f(x)=интеграл от (ln(1-x))dx
syms x;
f = int(log(1-x), x)

% 3. Построить график функции f(x)=x-(x), где с шагом 0,01 и 0,03 в декартовой системе координат.
x1 = -10:0.01:10;
x2 = -10:0.03:10;
y1 = x1 - (x1);
y2 = x2 - (x2);
figure;
subplot(2,1,1);
plot(x1, y1);
title('График функции f(x)=x-(x) с шагом 0.01');
subplot(2,1,2);
plot(x2, y2);
title('График функции f(x)=x-(x) с шагом 0.03');

% 4. Построить график функции f(x)=cos(6x), где с шагом 0.01 в полярной системе координат.
x = 0:0.01:2*pi;
r = cos(6*x);
figure;
polarplot(x, r);
title('График функции f(x)=cos(6x) в полярной системе координат');

% 5. Построить график функции f(x,y)=|x|+|y|-|x+y|, с шагом 0,1 в трехмерной системе координат.
[X, Y] = meshgrid(-10:0.1:10, -10:0.1:10);
Z = abs(X) + abs(Y) - abs(X + Y);
figure;
surf(X, Y, Z);
title('График функции f(x,y)=|x|+|y|-|x+y| в трехмерной системе координат');


===================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================

% Параметры модели Ферхюльста
r = 0.66; % коэффициент роста
K = 202; % предельная ёмкость среды

% Изначальные условия для Ферхюльста
N0 = 1; % начальное количество бактерий
t0 = 0; % начальное время
t1 = 10; % конечное время

% Шаг интегрирования для метода Эйлера
h = 0.01;

% Инициализация переменных для метода Эйлера
N = N0;
t = t0;

% Цикл интегрирования методом Эйлера для уравнения Ферхюльста
while t < t1
    dNdt = r * N * (1 - N/K); % уравнение Ферхюльста
    N = N + h * dNdt;
    t = t + h;
end

disp(['Популяция бактерий по модели Ферхюльста через 10 дней: ', num2str(N)]);

% Решение задачи Коши dx/dt = rx^2 с начальным условием x(0) = x0
x0 = 1; % начальное значение x
tspan = [0 10]; % временной интервал

% Определение дифференциального уравнения для задачи Коши
dxdt = @(t, x) r*x^2;

% Установка параметров погрешности для ode45
options = odeset('RelTol', 1e-6, 'AbsTol', 1e-9);

% Использование ode45 для численного решения дифференциального уравнения с установленными параметрами погрешности
[t, x_approx] = ode45(dxdt, tspan, x0, options);

% Вывод результатов задачи Коши
disp(['Популяция по решению задачи Коши через 10 единиц времени: ', num2str(x_approx(end))]);
clear 
clc 

% Параметры модели Ферхюльста
r = 0.66; % коэффициент роста
K = 202; % предельная ёмкость среды

% Изначальные условия для Ферхюльста
N0 = 1; % начальное количество бактерий
t0 = 0; % начальное время
t1 = 10; % конечное время

% Шаг интегрирования для метода Эйлера
h = 0.01;

% Инициализация переменных для метода Эйлера
N = N0;
t = t0;

% Цикл интегрирования методом Эйлера для уравнения Ферхюльста
while t < t1
    dNdt = r * N * (1 - N/K); % уравнение Ферхюльста
    N = N + h * dNdt;
    t = t + h;
end

disp(['Популяция бактерий по модели Ферхюльста через 10 дней: ', num2str(N)]);

% Решение задачи Коши dx/dt = rx^2 с начальным условием x(0) = x0
x0 = 1; % начальное значение x
tspan = [0 10]; % временной интервал

% Определение дифференциального уравнения для задачи Коши
dxdt = @(t, x) r*x^2;

% Установка параметров погрешности для ode45
options = odeset('RelTol', 1e-6, 'AbsTol', 1e-9);

% Использование ode45 для численного решения дифференциального уравнения с установленными параметрами погрешности
[t, x_approx] = ode45(dxdt, tspan, x0, options);

% Вывод результатов задачи Коши
disp(['Популяция по решению задачи Коши через 10 единиц времени: ', num2str(x_approx(end))]);


====================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================

#include <iostream>
using namespace std;

double f(double x) {
    return pow(x, 5) - pow(x, 4) + sin(pow(x, 5));
}

bool vipuklost(double u1, double u2, double u3) {
    double d1 = f(u1) - f(u2);
    double d2 = f(u3) - f(u2);
    if (d1 >= 0 && d2 >= 0 && d1 + d2 > 0)
        return 1;
    else
        return 0;
}

double parabolaMethod(double h, double u0, double a, double b) {
    int  i = 2;
    if (f(u0) < f(u0 + h))
        h *= -1;
    double u1 = u0 + h, u2 = u0 +  h * pow(2, i - 1);
    while (u2 >= a && u2 <= b && f(u1) >= f(u2)) {
        u0 = u1;
        u1 = u2;
        u2 = u0 +  h * pow(2, i - 1);
        cout << u2 << endl;
    }
    return f(u2);
}

int main() {
    double h = 0.0001, u0 = 1.3, a = 1.2, b = 2;
    cout <<  parabolaMethod(h, u0, a, b); 
    return 0;
}




====================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================


#include <iostream>
using namespace std;

double f(double x) {
    return pow(x, 5) - pow(x, 3);
}
double df(double x) {
    return 5 * pow(x, 4) - 3 * pow(x, 2);
}
double ddf(double x) {
    return 20 * pow(x, 3) - 6 * x;
}

double newtonMethod(double eps, double x0) {
    double last = df(x0);
    do {
        cout << abs(df(x0)) << endl;
        x0 = x0 - df(x0) / ddf(x0);
    } while (abs(df(x0)) > eps);
    return x0;
}

int main() {
    double eps = 0.000001, u0 = 0.2;
    cout << newtonMethod(eps, u0);
    return 0;
}
