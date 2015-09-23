#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <ctype.h>

pthread_t thread[4];
pthread_mutex_t lock;

typedef struct dict {
  char *word;
  int count;
  struct dict *next;
} dict_t;

typedef struct words
{
    dict_t *D;
    char w_buffer[1024];
   
}word_t;


char *make_word( char *word ) {
  return strcpy( malloc( strlen( word )+1 ), word );
}

void *make_dict(void *wordInput) {
  char * word = (char *) wordInput;

   pthread_mutex_lock (&lock);
  dict_t *nd = (dict_t *) malloc( sizeof(dict_t) );
  nd->word = make_word( word );
  nd->count = 1;
  nd->next = NULL;
  pthread_mutex_unlock (&lock);
  return nd;
}

void *insert_word( void * insert){

word_t *p = malloc(sizeof (*p));
p = (word_t *) insert;
dict_t *d =(dict_t *) p->D;
char * word = (char *)p->w_buffer;

  dict_t *nd;
  dict_t *pd = NULL;   
  dict_t *di = d;   

  while(di && ( strcmp(word, di->word ) >= 0) ) {
    if( strcmp( word, di->word ) == 0 ) {
      di->count++;       
      return d;           
    }
    pd = di;           
    di = di->next;
  }

  void * s;
  pthread_create(&thread[3], NULL, make_dict,  word);
  pthread_join(thread[3], &s);

  dict_t *newDict = malloc(sizeof(*newDict));
  newDict = (dict_t *) s;
 
  newDict->next = di;       
  if (pd) {
    pd->next = newDict;
    return d;           
  }
 
  return newDict;
}


void * print_dict( dict_t *d) {

  while (d) {
    printf("[%d] %s\n", d->count, d->word);
    d = d->next;
  }
}

int get_word( char *buf, int n, FILE *infile) {
  int inword = 0;
  int c; 
  while( (c = fgetc(infile)) != EOF ) {
    if (inword && !isalpha(c)) {
      buf[inword] = '\0';   
      return 1;
    }
    if (isalpha(c)) {
      buf[inword++] = c;
    }
  }
  return 0;           
}





void *words( void * in ) {

  FILE *infile = (FILE * ) in;
  dict_t *wd = NULL;
  char wordbuf[1024] ;

  void * l;

  word_t *t = malloc(sizeof (*t));
 

int d=0;
 
  while( get_word( wordbuf, 1024, infile ) ) {

      t->D = wd;

     strcpy(t->w_buffer, wordbuf);
     pthread_create(&thread[2], NULL, insert_word,t);
      pthread_join(thread[2], &l);
      wd = l;

  }

 
  free (t);
  return  wd;
}

int
main( int argc, char *argv[] ) {
  dict_t *d = NULL;
  FILE *infile = stdin;
  if (argc >= 2) {
    infile = fopen (argv[1],"r");
  }
  if( !infile ) {
    printf("Unable to open %s\n",argv[1]);
    exit( EXIT_FAILURE );
  }

 void * v;

 int y = pthread_create(&thread[0], NULL, words, infile);
 pthread_join(thread[0], &v);

 int x = pthread_create(&thread[1], NULL, print_dict,  v);

 
 pthread_join(thread[1], NULL);

  fclose( infile );
}
