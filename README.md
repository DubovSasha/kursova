# kursova
#include <stdio.h>
#include <locale.h>
#include <stdlib.h>
#include <string.h>

#define MAX_RECORDS 100
#define MAX_STR_LEN 50

typedef struct {
    char manufacturer[MAX_STR_LEN];
    char watchType[MAX_STR_LEN];
    int simSupport; // 1 — да, 0 — нет
    char osCompatibility[MAX_STR_LEN];
    float screenDiagonal;
} SmartWatch;

SmartWatch records[MAX_RECORDS];
int recordCount = 0;

void addRecord() {
    if (recordCount >= MAX_RECORDS) {
        printf("\nДостигнут предел записей. Добавление новых записей невозможно.\n");
        return;
    }

    SmartWatch newRecord;
    printf("\nВведите производителя: ");
    scanf("%s", newRecord.manufacturer);

    printf("Введите тип часов: ");
    scanf("%s", newRecord.watchType);

    // Проверка корректности ввода поддержки SIM-карты
    do {
        printf("Поддержка SIM (1 — Да, 0 — Нет): ");
        if (scanf("%d", &newRecord.simSupport) != 1 || (newRecord.simSupport != 0 && newRecord.simSupport != 1)) {
            printf("Ошибка: введите 1 для 'Да' или 0 для 'Нет'.\n");
            while (getchar() != '\n'); // Очистка буфера ввода
        }
        else {
            break;
        }
    } while (1);

    printf("Введите совместимость с ОС: ");
    scanf("%s", newRecord.osCompatibility);

    printf("Введите диагональ экрана (в дюймах): ");
    scanf("%f", &newRecord.screenDiagonal);

    records[recordCount++] = newRecord;
    printf("\nЗапись успешно добавлена.\n");
}

void addMultipleRecords() {
    int numRecords;
    printf("\nВведите количество записей для добавления: ");
    scanf("%d", &numRecords);

    if (recordCount + numRecords > MAX_RECORDS) {
        printf("\nНедостаточно места для добавления %d записей. Доступно место для %d записей.\n", numRecords, MAX_RECORDS - recordCount);
        return;
    }

    for (int i = 0; i < numRecords; i++) {
        printf("\nДобавление записи %d из %d:\n", i + 1, numRecords);
        addRecord();
    }
    printf("\nВсе записи успешно добавлены.\n");
}

void editRecord() {
    char manufacturer[MAX_STR_LEN];
    printf("\nВведите производителя для редактирования: ");
    scanf("%s", manufacturer);

    int found = 0;
    for (int i = 0; i < recordCount; i++) {
        if (strcmp(records[i].manufacturer, manufacturer) == 0) {
            printf("\nРедактирование записи:\n");
            printf("Текущий производитель: %s\n", records[i].manufacturer);
            printf("Введите новый производитель: ");
            scanf("%s", records[i].manufacturer);

            printf("Текущий тип часов: %s\n", records[i].watchType);
            printf("Введите новый тип часов: ");
            scanf("%s", records[i].watchType);

            printf("Текущая поддержка SIM: %s\n", records[i].simSupport ? "Да" : "Нет");
            printf("Введите новую поддержку SIM (1 — Да, 0 — Нет): ");
            scanf("%d", &records[i].simSupport);

            printf("Текущая совместимость с ОС: %s\n", records[i].osCompatibility);
            printf("Введите новую совместимость с ОС: ");
            scanf("%s", records[i].osCompatibility);

            printf("Текущая диагональ экрана: %.2f дюйма\n", records[i].screenDiagonal);
            printf("Введите новую диагональ экрана (в дюймах): ");
            scanf("%f", &records[i].screenDiagonal);

            printf("\nЗапись успешно обновлена.\n");
            found = 1;
            break;
        }
    }

    if (!found) {
        printf("\nЗапись с производителем '%s' не найдена.\n", manufacturer);
    }
}

void searchRecord() {
    char manufacturer[MAX_STR_LEN];
    printf("\nВведите производителя для поиска: ");
    scanf("%s", manufacturer);

    int found = 0;
    for (int i = 0; i < recordCount; i++) {
        if (strcmp(records[i].manufacturer, manufacturer) == 0) {
            printf("\nЗапись найдена:\n");
            printf("Производитель: %s\n", records[i].manufacturer);
            printf("Тип часов: %s\n", records[i].watchType);
            printf("Поддержка SIM: %s\n", records[i].simSupport ? "Да" : "Нет");
            printf("Совместимость с ОС: %s\n", records[i].osCompatibility);
            printf("Диагональ экрана: %.2f дюйма\n", records[i].screenDiagonal);
            found = 1;
        }
    }

    if (!found) {
        printf("\nЗаписи с производителем '%s' не найдены.\n", manufacturer);
    }
}

void saveToFile() {
    FILE* file = fopen("smartwatches.dat", "wb");
    if (file == NULL) {
        printf("\nОшибка при открытии файла для записи.\n");
        return;
    }

    fwrite(&recordCount, sizeof(int), 1, file);
    fwrite(records, sizeof(SmartWatch), recordCount, file);

    fclose(file);
    printf("\nЗаписи успешно сохранены в файл.\n");
}

void loadFromFile() {
    FILE* file = fopen("smartwatches.dat", "rb");
    if (file == NULL) {
        printf("\nОшибка при открытии файла для чтения или файл не существует.\n");
        return;
    }

    fread(&recordCount, sizeof(int), 1, file);
    fread(records, sizeof(SmartWatch), recordCount, file);

    fclose(file);
    printf("\nЗаписи успешно загружены из файла.\n");
}

void printRecords() {
    printf("\nВсе записи:\n");
    for (int i = 0; i < recordCount; i++) {
        printf("            #%i        \n", i + 1);
        printf("\nПроизводитель: %s\n", records[i].manufacturer);
        printf("Тип часов: %s\n", records[i].watchType);
        printf("Поддержка SIM: %s\n", records[i].simSupport ? "Да" : "Нет");
        printf("Совместимость с ОС: %s\n", records[i].osCompatibility);
        printf("Диагональ экрана: %.2f дюйма\n", records[i].screenDiagonal);
    }
}

void sortRecords(int criteria) {
    // Сортировка по производителю или диагонали экрана
    for (int i = 0; i < recordCount - 1; i++) {
        for (int j = 0; j < recordCount - i - 1; j++) {
            int compareResult = 0;
            if (criteria == 1) {
                compareResult = strcmp(records[j].manufacturer, records[j + 1].manufacturer);
            }
            else if (criteria == 2) {
                if (records[j].screenDiagonal > records[j + 1].screenDiagonal) {
                    compareResult = 1;
                }
                else if (records[j].screenDiagonal < records[j + 1].screenDiagonal) {
                    compareResult = -1;
                }
            }

            if (compareResult > 0) {
                SmartWatch temp = records[j];
                records[j] = records[j + 1];
                records[j + 1] = temp;
            }
        }
    }
    printf("\nЗаписи успешно отсортированы.\n");
}

int main() {
    setlocale(LC_ALL, "RUS");
    int choice;

    do {
        printf("\nСистема управления записями о смарт-часах\n");
        printf("1. Добавить запись\n");
        printf("2. Добавить несколько записей\n");
        printf("3. Найти запись\n");
        printf("4. Редактировать запись\n");
        printf("5. Сохранить в файл\n");
        printf("6. Загрузить из файла\n");
        printf("7. Показать все записи\n");
        printf("8. Сортировка записей\n");
        printf("9. Выход\n");
        printf("Введите ваш выбор: ");
        scanf("%d", &choice);

        switch (choice) {
        case 1:
            addRecord();
            break;
        case 2:
            addMultipleRecords();
            break;
        case 3:
            searchRecord();
            break;
        case 4:
            editRecord();
            break;
        case 5:
            saveToFile();
            break;
        case 6:
            loadFromFile();
            break;
        case 7:
            printRecords();
            break;
        case 8:
            printf("\nВыберите критерий сортировки:\n");
            printf("1. По производителю\n");
            printf("2. По диагонали экрана\n");
            int criteria;
            scanf("%d", &criteria);
            sortRecords(criteria);
            break;
        case 9:
            printf("\nЗавершение программы. До свидания!\n");
            break;
        default:
            printf("\nНеверный выбор. Пожалуйста, попробуйте снова.\n");
        }
    } while (choice != 9);

    return 0;
}
