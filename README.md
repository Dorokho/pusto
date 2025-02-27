# pusto
#include <stdio.h>

int main() {
    int num1, num2, result;
    char op;
    
    printf("Введите выражение (например, 5 + 3): ");
    if (scanf("%d %c %d", &num1, &op, &num2) != 3) {
        printf("Ошибка ввода!\n");
        return 1;
    }
    
    switch (op) {
        case '+':
            result = num1 + num2;
            break;
        case '-':
            result = num1 - num2;
            break;
        case '*':
            result = num1 * num2;
            break;
        case '/':
            if (num2 == 0) {
                printf("Ошибка: деление на ноль!\n");
                return 1;
            }
            result = num1 / num2;
            break;
        case '%':
            if (num2 == 0) {
                printf("Ошибка: деление на ноль!\n");
                return 1;
            }
            result = num1 % num2;
            break;
        default:
            printf("Ошибка: неизвестная операция!\n");
            return 1;
    }
    
    printf("Результат: %d\n", result);
    return 0;
}
