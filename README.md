# Kamil
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


Wykonałem kod wzoryjąc sie na  
http://radzio.dxp.pl/hd44780/
przerobiłem ją tak , aby porty które używam.
Mam  wyświetlacz na którym w 1 linijce pokazuje się
aktualna temperatura pieca ( bo jest to sterownik do pompy pieca
i linijkę niżej jest temperatura którą ustawia się tamtymi dwoma przyciskami
zakres regulacji od 25 stopni do 80 stopni
jeżeli temperatura pieca przekroczy zakres niższej wartości zacznie się praca pompy cyrkulacyjnej
zastosowanie jest takie żeby pompa nie chodziła cały czas tylko wtedy jak już jest rozpalone w piecu
