# Projekt Pieca
```
#include <avr/io.h>
#include <util/delay.h>
#include <avr/HD44780.h>
#include "avr/HD44780.c"
#include <stdlib.h>

#define Relay (1<<PB0)
#define LED (1<<PB1)
#define ButtonUp (1<<PC0)
#define ButtonDown (1<<PC1)

void init_ADC(){
	ADMUX |= (1 << REFS0);
	ADCSRA |= (1 << ADPS1) | (1 << ADPS0);
	ADCSRA |= (1 << ADEN);
}


int main(void){
DDRB |= Relay;
DDRB |= LED;
	LCD_Clear();
	LCD_GoTo(0,0);
	LCD_WriteText("Temperatura:");
	LCD_GoTo(0,1);
	LCD_WriteText("Wylaczanie:");
	int temp=0;
	char tempCHAR[3];
	int ust=25;
	char ustCHAR[3];
	int potencjometr=0;
		while(1){
			ADCSRA |= (1 << ADSC);
			loop_until_bit_is_clear(ADCSRA, ADSC);
			_delay_ms(5);
			potencjometr= ADC;
			temp=((1024-potencjometr)/20)+25;//10k przy 25 stopniach czyli wartosc ADC bedzie w zakresie 1020-1024
			itoa(temp, tempCHAR, 10);
			if(!(PINC & ButtonUp)){
				if(ust>=80){
					ust=25;
				}
				else{
					ust++;
				}
				_delay_ms(200);
			}
			if(!(PINC & ButtonDown)){
				if(ust<=25){
					ust=80;
				}
				else{
					ust--;
				}
				_delay_ms(200);
			}
			itoa(ust, ustCHAR, 10);
			LCD_GoTo(13,0);
			LCD_WriteText(tempCHAR);
			LCD_GoTo(13,1);
			LCD_WriteText(ustCHAR);
			if(temp<=ust){
				PORTB |= Relay;
				PORTB |= LED;
			}
			if(temp>ust){
				if(PINB & Relay){
				PORTB ^= Relay;
				PORTB ^= LED;
				}
			}
		}
}

```

Wykonałem kod wzorując sie na  
http://radzio.dxp.pl/hd44780/
Przerobiłem ją tak ,aby używać potrzebnych mi portów
Mam  wyświetlacz na którym w 1 linijce pokazuje się
aktualna temperatura pieca ( bo jest to sterownik do pompy pieca
i linijkę niżej jest temperatura którą ustawia się tamtymi dwoma przyciskami
zakres regulacji od 25 stopni do 80 stopni
Jeżeli temperatura pieca przekroczy zakres niższej wartości zacznie się praca pompy cyrkulacyjnej
zastosowanie jest takie żeby pompa nie chodziła cały czas tylko wtedy jak już jest rozpalone w piecu

Piki eagle i Kod Programu
[IglaKOD.zip](https://github.com/UMGKamil/Kamil/files/8056009/IglaKOD.zip)
[iglaEAGLE.zip](https://github.com/UMGKamil/Kamil/files/8056010/iglaEAGLE.zip)



![Zrzut ekranu 2022-02-13 173402](https://user-images.githubusercontent.com/94539637/153762976-1db44388-aa82-4196-90e2-8316890daace.jpg)


Potrzebne elementy 

```
```
Mikrokontroler
ZAsilanie 12-5v DC
Wyświetlacz LCD
ISP
Przyciski
Prezkaźnik i dioda led
Termistor


![Zrzut ekranu 2022-02-13 173513](https://user-images.githubusercontent.com/94539637/153763030-82c03527-0f3a-4181-89d5-64d07d2bc148.jpg)

