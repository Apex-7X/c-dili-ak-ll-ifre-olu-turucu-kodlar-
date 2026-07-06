#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <time.h>

// karakter havuzları
const char kucuk[] = "abcdefghijklmnopqrstuvwxyz";
const char buyuk[] = "ABCDEFGHIJKLMNOPQRSTUVWXYZ";
const char rakam[]  = "0123456789";
const char ozel[]   = "!@#$%^&*()-_=+?/";

// input buffer temizleme (sonsuz döngüyü engeller)
void temizleBuffer() {
    int c;
    while ((c = getchar()) != '\n' && c != EOF);
}

// şifre üretme fonksiyonu
void sifreUret(char *sifre, int uzunluk, int seviye) {

    char havuz[300] = "";

    strcat(havuz, kucuk);
    strcat(havuz, buyuk);

    if (seviye >= 2) strcat(havuz, rakam);
    if (seviye >= 3) strcat(havuz, ozel);

    int h = strlen(havuz);

    sifre[0] = kucuk[rand() % strlen(kucuk)];
    sifre[1] = buyuk[rand() % strlen(buyuk)];

    if (seviye >= 2)
        sifre[2] = rakam[rand() % strlen(rakam)];
    else
        sifre[2] = kucuk[rand() % strlen(kucuk)];

    if (seviye >= 3)
        sifre[3] = ozel[rand() % strlen(ozel)];
    else
        sifre[3] = buyuk[rand() % strlen(buyuk)];

    for (int i = 4; i < uzunluk; i++) {
        sifre[i] = havuz[rand() % h];
    }

    // karıştırma
    for (int i = uzunluk - 1; i > 0; i--) {
        int j = rand() % (i + 1);
        char t = sifre[i];
        sifre[i] = sifre[j];
        sifre[j] = t;
    }

    sifre[uzunluk] = '\0';
}

// güvenlik puanı
int guvenlikPuani(int seviye, int uzunluk) {
    int puan = seviye * 30;

    if (uzunluk >= 8)  puan += 10;
    if (uzunluk >= 12) puan += 20;
    if (uzunluk >= 16) puan += 30;

    if (puan > 100) puan = 100;

    return puan;
}

int main() {

    srand(time(NULL));

    int uzunluk, seviye;
    char sifre[100];
    char cevap, tekrar;

    while (1) {

        // 🔐 UZUNLUK SEÇİMİ (GÜVENLİ)
        while (1) {
            printf("\nUzunluk secmek ister misin? (E/H): ");

            if (scanf(" %c", &cevap) != 1) {
                printf("HATA: Gecersiz giris!\n");
                temizleBuffer();
                continue;
            }

            if (cevap == 'E' || cevap == 'e' || cevap == 'H' || cevap == 'h')
                break;

            printf("HATA: Sadece E veya H gir!\n");
        }

        if (cevap == 'E' || cevap == 'e') {

            while (1) {
                printf("Uzunluk (4-25): ");

                if (scanf("%d", &uzunluk) != 1) {
                    printf("HATA: Sayi girmelisin! Harf kabul edilmez.\n");
                    temizleBuffer();
                    continue;
                }

                if (uzunluk < 4) {
                    printf("HATA: 4'ten kucuk olamaz!\n");
                }
                else if (uzunluk > 25) {
                    printf("HATA: 25'ten buyuk olamaz!\n");
                }
                else {
                    break;
                }
            }

        } else {
            uzunluk = 8 + rand() % 10;
        }

        // 🔐 SEVİYE SEÇİMİ (GÜVENLİ)
        while (1) {
            printf("\nSeviye secmek ister misin? (E/H): ");

            if (scanf(" %c", &cevap) != 1) {
                printf("HATA: Gecersiz giris!\n");
                temizleBuffer();
                continue;
            }

            if (cevap == 'E' || cevap == 'e' || cevap == 'H' || cevap == 'h')
                break;

            printf("HATA: Sadece E veya H gir!\n");
        }

        if (cevap == 'E' || cevap == 'e') {

            while (1) {
                printf("Seviye (1-3): ");

                if (scanf("%d", &seviye) != 1) {
                    printf("HATA: Sayi girmelisin! Harf kabul edilmez.\n");
                    temizleBuffer();
                    continue;
                }

                if (seviye < 1) {
                    printf("HATA: 1'den kucuk olamaz!\n");
                }
                else if (seviye > 3) {
                    printf("HATA: 3'ten buyuk olamaz!\n");
                }
                else {
                    break;
                }
            }

        } else {
            seviye = 2 + rand() % 2;
        }

        // şifre üret
        sifreUret(sifre, uzunluk, seviye);

        // çıktı
        printf("\n====================\n");
        printf("SIFRENIZ: %s\n", sifre);
        printf("UZUNLUK: %d\n", uzunluk);
        printf("SEVIYE: %d\n", seviye);
        printf("PUAN: %d/100\n", guvenlikPuani(seviye, uzunluk));
        printf("====================\n");

        printf("\nYeni sifre? (E/H): ");

        while (scanf(" %c", &tekrar) != 1 ||
               !(tekrar=='E'||tekrar=='e'||tekrar=='H'||tekrar=='h')) {
            printf("HATA: Sadece E veya H!\n");
            temizleBuffer();
        }

        if (tekrar == 'H' || tekrar == 'h') {
            printf("\nSistem kapatildi.\n");
            break;
        }
    }

    return 0;
}
