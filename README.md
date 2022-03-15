

#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <unistd.h>
#include <errno.h>

int main()
{
  pid_t fils_pid ;
  int de_fils[2] ;
  int m_fils[2] ;
  int mfils,defils ;

  /* ----------- Creation  Pipes -------------*/
  if (pipe(de_fils) || pipe(m_fils)){
    Perror(" Erreur de creation de pipes ") ;
    Exit(errno) ;
  }
    /*-------creation du processus fils------*/
  if ( ( fils_pid=fork() ) == -1 ) {
    Perror(" Erreur de creation du fils ") ;
    Exit(errno) ;
  }

  if ( fils_pid ) {
    char c ='p';
    /* ----------- Parent --------------------*/
    Printf("Parent. It knows the child id = %d\ ",fils_pid) ;

    Close(de_fils[1]) ;
    Close(m_fils[0]) ;

    if ( write(m_fils[1],&c,1) != 1 ) {
       Perror("Parent : impossible d'ecrir au fils") ;
       Exit(errno) ;
     }

    if ( read(de_fils[0],&c,1) != 1 ) {
      Perror(" Parent : impossible de lire dans le fils ") ;
      Exit(errno) ;
    }

    Printf(" Parent : a obtenu du caractere '%c' dans le fils " ,c) ;
  }
  /* ------------- fils ---------------------*/
  else {
     char c;
    Close(de_fils[0]) ;
    Close(m_fils[1]) ;
      if ( read(m_fils[0],&c,1) != 1 ) {
      Perror(" fils: impossible de lire dans le parent ") ;
      Exit(errno) ;
    }

    Printf(" fils : got character ‘%c’ from parent",c) ;

    c ='c' ;
    if ( write(de_fils[1],&c,1) != 1 ) {
      Perror("fils: impossible d ecrir au parent ") ;
      Exit(errno) ;
    }

  }
    return 0 ;
}
