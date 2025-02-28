#include <stdio.h>
#include <stdlib.h>
#include <ctype.h>

const char *expr;

// Прототипы функций
int parse_expression();
int parse_term();
int parse_factor();

// Функция пропуска пробелов
void skip_whitespace() {
    while (*expr == ' ') expr++;
}

// Функция для обработки чисел и скобок
int parse_factor() {
    skip_whitespace();
    int result = 0;
    if (*expr == '(') {
        expr++;
        result = parse_expression();
        if (*expr == ')') expr++;
    } else {
        while (isdigit(*expr)) {
            result = result * 10 + (*expr - '0');
            expr++;
        }
    }
    skip_whitespace();
    return result;
}

// Функция для обработки умножения и деления
int parse_term() {
    int result = parse_factor();
    while (*expr == '*' || *expr == '/') {
        char op = *expr;
        expr++;
        int next_factor = parse_factor();
        if (op == '*')
            result *= next_factor;
        else if (op == '/') {
            if (next_factor == 0) {
                printf("Ошибка: деление на ноль!\n");
                exit(EXIT_FAILURE);
            }
            result /= next_factor;
        }
    }
    return result;
}

// Функция для обработки сложения и вычитания
int parse_expression() {
    int result = parse_term();
    while (*expr == '+' || *expr == '-') {
        char op = *expr;
        expr++;
        int next_term = parse_term();
        if (op == '+')
            result += next_term;
        else if (op == '-')
            result -= next_term;
    }
    return result;
}

int main() {
    char input[100];
    printf("Введите выражение: ");
    fgets(input, sizeof(input), stdin);
    expr = input;
    int result = parse_expression();
    printf("Результат: %d\n", result);
    return 0;
}
