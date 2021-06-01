#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>
#include "Practical.h"

#define BUFSIZE 1024

int main(int argc, char *argv[])
{
	if ( argc < 3 || argc > 4) // 명령어 인자의 정확한 개수 확인 
		DieWithUserMessage("parameter(s)",
			"<Server Address> <Echo Word> [ <Server Port>]");

	char *servIP = argv[1]; //첫 번째 인자:서버 IP 주소(dotted 형식)
	char *echoString = argv[2]; //두 번째 인자: 보내려는 애코 문자열

	// 세 번째 인자(선택): 서버포트(숫자형식). 7이 잘 알려진 포트로 생략 시 7을 사용
	in_port_t servPort = (argc == 4) ? atoi(argv[3]) : 7;

	//TCP를 사용하여 안정된 스트림 소켓 생성 
	int sock = socket(AF_INET, SOCK_STREAM, IPPROTO_TCP);
	if (sock < 0)
		DieWithSystemMessage("socket() failed");

	//서버 주소 구조체 생성 
	struct sockaddr_in servAddr;             //서버주소
	memset(&servAddr, 0, sizeof(servAddr)); //0으로 구조체 초기화
	servAddr.sin_family = AF_INET;           // IPv4 주소 패밀리
	// 주소 변환
	int rtnVal = inet_pton(AF_INET, servIP, &servAddr.sin_addr.s_addr);
	if (rtnVal == 0)
		DieWithUserMessage("inet_pton() failed", "invalid Address string");
	else if (rtnVal < 0)
		DieWithSystemMessage("inet_pton() failed");
	servAddr.sin_port = htons(servPort); //서버포트
		// 에코 서버에 연결 설정
	if (connect(sock, (struct sockaddr *) &servAddr, sizeof(servAddr)) < 0)
		DieWithSystemMessage("connect() failed");
		size_t echoStringLen = strlen(echoString); // 입력받은 문자열의 길이를 확인
		// 서버에 에코 문자열 전송 
	ssize_t numBytes = send(sock, echoString, echoStringLen, 0);
	if (numBytes < 0)
		DieWithSystemMessage("send() failed");
	else if (numBytes != echoStringLen)
		DieWithUserMessage("send()", "sent unexpected number of bytes");
		// 서버로부터 동일한 문자열 수신
	unsigned int totalBytesRcvd = 0; //수신한 문자 개수 
	 fputs("Received: ", stdout);      // 돌려받은 에코 문자열 출력을 위한 설정
	while (totalBytesRcvd < echoStringLen)
	{
		char buffer[BUFSIZE]; // 입/출력 버퍼
		/* 버퍼 크기(byte) 만큼 서버로 부터 수신
		(널 분자를 위해 1 바이트 남겨놓음) */
		numBytes = recv(sock, buffer, BUFSIZE - 1, 0);
		if (numBytes < 0)
			DieWithSystemMessage("recv() failed");
		else if (numBytes == 0)
			DieWithUserMessage("recv()", "connection closed prematurely");
		totalBytesRcvd += numBytes; // 총 받은 크기를 기록
		buffer[numBytes] = '\0';      //  널 분자를 추가하여 문자열 완성!
		fputs(buffer, stdout);       // 에코 버퍼를 출력

	}

	fputc('\n', stdout); // 마지막으로 개행문자 출력

	close(sock);
	exit (0);
}
