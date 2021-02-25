#define F_CPU 16000000UL

#include <avr/io.h>
#include <avr/interrupt.h>
#include <util/delay.h>

#define OC2B  PD3
#define PB2	2
#define PB3	3

ISR(TIMER2_OVF_vect){
	
}

int main(void)
{
   // DDRB = 0xff;					//	아두이노 작동 확인용
   
	DDRD = 1<<PD3;					//	출력으로 PD3 출력(OC2B) 
	PORTB = 1<<PB2 | 1<<PB3;		//	내부 풀업저항 설정

	TCCR2A |= 1<<WGM21 | 1<<WGM20;		// 0,1,1 Fast PWM
	TCCR2A |= 1<<COM2B1;			//Clear OC2B on Compare Match, set OC2B at BOTTOM(non-inverting mode)
									//OC2B가 작동중에 비교일치 될 때 0으로 set
	TCCR2B |= 1<<CS22 | 1<<CS21;	//8bit라서 TCCR2A랑 나눠서 사용함, 256분주비 설정						
	TIMSK2 = 1<<TOIE2;
	OCR2B = 0;	
					

	while (1)
	{
		if (PINB & (1<<PB2)){
			_delay_ms(10);
			(OCR2B==255) ? OCR2B=255 : OCR2B++;
		}
		if (PINB & (1<<PB3)){
			_delay_ms(10);
			(OCR2B==0) ? OCR2B=0 : OCR2B--;
		}
	}
	return 0;
}
