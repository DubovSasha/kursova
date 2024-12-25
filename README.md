# kursova
#include <stdio.h>
#include <locale.h>
#include <stdlib.h>
#include <string.h>

// Максимальное количество записей
#define MAX_RECORDS 100
// Имя файла для сохранения данных
#define FILENAME "smartwatches.txt"

// Структура для хранения информации о смарт-часах
typedef struct {
    char manufacturer[50]; // Производитель
    char type[50];         // Тип часов
    int supportsSIM;       // Поддержка SIM-карты (1 - да, 0 - нет)
    char osCompatibility[50]; // Совместимость с ОС
    float screenDiagonal;     // Диагональ экрана
} SmartWatch;

// Массив записей и количество записей
SmartWatch records[MAX_RECORDS];
int recordCount = 0;

// Функция загрузки записей из файла
void loadRecordsFromFile() {
    FILE* file = fopen(FILENAME, "r");
    if (file) {
        // Читаем данные из файла в массив
        recordCount = fread(records, sizeof(SmartWatch), MAX_RECORDS, file);
        fclose(file);
    }
}

// Функция сохранения записей в файл
void saveRecordsToFile() {
    FILE* file = fopen(FILENAME, "w");
    if (file) {
        // Записываем массив в файл
        fwrite(records, sizeof(SmartWatch), recordCount, file);
        fclose(file);
    }
}

// Функция добавления новой записи
void addRecord() {
    if (recordCount >= MAX_RECORDS) {
        printf("Невозможно добавить больше записей. Достигнут максимальный лимит.\n");
        return;
    }

    SmartWatch sw;
    // Ввод данных пользователя
    printf("Введите производителя: ");
    scanf("%49s", sw.manufacturer);
    printf("Введите тип часов: ");
    scanf("%49s", sw.type);
    printf("Поддержка SIM-карты (1 - да, 0 - нет): ");
    scanf("%d", &sw.supportsSIM);
    printf("Введите совместимость с ОС: ");
    scanf("%49s", sw.osCompatibility);
    printf("Введите диагональ экрана: ");
    scanf("%f", &sw.screenDiagonal);

    // Добавляем запись в массив
    records[recordCount++] = sw;
    saveRecordsToFile(); // Сохраняем изменения в файл
    printf("Запись успешно добавлена!\n");
}

// Функция удаления записи
void deleteRecord() {
    if (recordCount == 0) {
        printf("Нет записей для удаления.\n");
        return;
    }

    int index;
    printf("Введите номер записи для удаления (1-%d): ", recordCount);
    scanf("%d", &index);

    if (index < 1 || index > recordCount) {
        printf("Некорректный номер записи.\n");
        return;
    }

    // Удаляем запись, сдвигая оставшиеся записи влево
    for (int i = index - 1; i < recordCount - 1; i++) {
        records[i] = records[i + 1];
    }
    recordCount--; // Уменьшаем количество записей
    saveRecordsToFile(); // Сохраняем изменения в файл
    printf("Запись удалена успешно.\n");
}

// Функция вывода всех записей
void printRecords() {
    if (recordCount == 0) {
        printf("Нет записей для отображения.\n");
        return;
    }

    for (int i = 0; i < recordCount; i++) {
        printf("%d. Производитель: %s, Тип: %s, Поддержка SIM: %s, Совместимость с ОС: %s, Диагональ экрана: %.2f\n",
            i + 1,
            records[i].manufacturer,
            records[i].type,
            records[i].supportsSIM ? "Да" : "Нет",
            records[i].osCompatibility,
            records[i].screenDiagonal);
    }
}

// Функция сравнения для сортировки по производителю
int compareByManufacturer(const void* a, const void* b) {
    return strcmp(((SmartWatch*)a)->manufacturer, ((SmartWatch*)b)->manufacturer);
}

// Функция сравнения для сортировки по типу часов
int compareByType(const void* a, const void* b) {
    return strcmp(((SmartWatch*)a)->type, ((SmartWatch*)b)->type);
}

// Функция сортировки записей
void sortRecords() {
    int choice;
    printf("Выберите критерий сортировки:\n");
    printf("1. По производителю\n");
    printf("2. По типу часов\n");
    printf("Введите ваш выбор: ");
    scanf("%d", &choice);

    // Выбор критерия сортировки
    if (choice == 1) {
        qsort(records, recordCount, sizeof(SmartWatch), compareByManufacturer);
        printf("Записи отсортированы по производителю.\n");
    }
    else if (choice == 2) {
        qsort(records, recordCount, sizeof(SmartWatch), compareByType);
        printf("Записи отсортированы по типу часов.\n");
    }
    else {
        printf("Некорректный выбор!\n");
    }
}

// Функция поиска записей по производителю
void searchByManufacturer() {
    char manufacturer[50];
    printf("Введите производителя для поиска: ");
    scanf("%49s", manufacturer);

    int found = 0;
    for (int i = 0; i < recordCount; i++) {
        if (strcmp(records[i].manufacturer, manufacturer) == 0) {
            printf("Найдена запись: Производитель: %s, Тип: %s, Поддержка SIM: %s, Совместимость с ОС: %s, Диагональ экрана: %.2f\n",
                records[i].manufacturer,
                records[i].type,
                records[i].supportsSIM ? "Да" : "Нет",
                records[i].osCompatibility,
                records[i].screenDiagonal);
            found = 1;
        }
    }

    if (!found) {
        printf("Записи с указанным производителем не найдены.\n");
    }
}

// Функция поиска записей по типу часов
void searchByType() {
    char type[50];
    printf("Введите тип часов для поиска: ");
    scanf("%49s", type);

    int found = 0;
    for (int i = 0; i < recordCount; i++) {
        if (strcmp(records[i].type, type) == 0) {
            printf("Найдена запись: Производитель: %s, Тип: %s, Поддержка SIM: %s, Совместимость с ОС: %s, Диагональ экрана: %.2f\n",
                records[i].manufacturer,
                records[i].type,
                records[i].supportsSIM ? "Да" : "Нет",
                records[i].osCompatibility,
                records[i].screenDiagonal);
            found = 1;
        }
    }

    if (!found) {
        printf("Записи с указанным типом часов не найдены.\n");
    }
}

// Главная функция программы
int main() {
    setlocale(LC_ALL, "RUS");
    loadRecordsFromFile(); // Загрузка данных при старте программы

    int choice;
    do {
        // Главное меню
        printf("\nУправление смарт-часами\n");
        printf("1. Добавить запись\n");
        printf("2. Показать записи\n");
        printf("3. Сортировать записи\n");
        printf("4. Найти запись по производителю\n");
        printf("5. Найти запись по типу часов\n");
        printf("6. Удалить запись\n");
        printf("7. Выйти\n");
        printf("Введите ваш выбор: ");
        scanf("%d", &choice);

        // Обработка выбора пользователя
        switch (choice) {
        case 1:
            addRecord(); // Добавить запись
            break;
        case 2:
            printRecords(); // Печать всех записей
            break;
        case 3:
            sortRecords(); // Сортировка записей
            break;
        case 4:
            searchByManufacturer(); // Поиск по производителю
            break;
        case 5:
            searchByType(); // Поиск по типу часов
            break;
        case 6:
            deleteRecord(); // Удаление записи
            break;
        case 7:
            saveRecordsToFile(); // Сохранение перед выходом
            printf("Выход из программы.\n");
            break;
        default:
            printf("Некорректный выбор. Попробуйте снова.\n");
        }
    } while (choice != 7);

    return 0;
}
