//Author:Cody Mercer
//Direct Cache Simulator

//Header files

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#define ARGC 5
#define SZ_MAX_BUFF 11
#define SZ_WORD 32
#define TRUE 1
#define FALSE 0

int main(int argc, char *argv[]){	
	
	//cmd line arguments submitted at compile (n m on/off testFile)
	//all required and defined variables and file handling
	
	char *tracer;
	char address[SZ_MAX_BUFF] = {0};	
	FILE *memory;
	int sizecache, sizeblock, numblock;
	unsigned int bitindex = 0;
	unsigned int bittag = 0;
	unsigned int bitcache = 0;
	unsigned int bitoffset = 0;
	unsigned int caindex = 0;
	unsigned int catag = 0;
	unsigned int cache_tag = 0;
	unsigned int caoffset = 0;
	unsigned int decadd = 0;
	static int totalhit = 0;
	static int totalmiss = 0;
	static int totalaccess = 0;
	static double missratio = 0;
	char isTrace = FALSE;
	char isValid = FALSE;
	char isHit = FALSE;
	char *hexadd;
	int lcv;
	
	//Return values for improper argument input
	
	if(argc != ARGC) {return -1;}
	if((bitcache = atoi(argv[1])) <= 0){return -2;}
	if((bitoffset = atoi(argv[2])) <= 0 || bitoffset > bitcache) {return -3;}
	sizecache = (2 << (bitcache - 1));
	sizeblock = (2 << (bitoffset - 1));
	numblock = sizecache / sizeblock;
	bitindex = bitcache - bitoffset;
	
	if (bitindex == 0){bitindex = 1;}
	bittag = SZ_WORD - bitoffset - bitindex;
	tracer = argv[3];
	
	int strlgth = strlen(tracer);
	if (strlgth > 3 || strlgth < 2) {return - 4;}
	
	//Define trace argument values
	
	if (strlgth == 2) {
		if(!(tracer[0] == 'o' || tracer[0] == 'O')){return -4;}
		if(!(tracer[1] == 'n' || tracer[1] == 'N')){return -4;}
		if (tracer[1] == 'n') {
			isTrace = TRUE;
			printf("%8s %8s %8s %8s %4s %7s %7s %7s %9s\n", "Address", "Tag", "Block", "Tag", "H/M", "Hit", "Miss", "Access", "Miss Ratio");}
	}
	
	if (strlgth == 3) {
		if (! (tracer[2] == 'f' || tracer[2] == 'F')) {return - 4;}
	}
	
	if((memory = fopen(argv[4], "r")) == NULL){
	    printf("error with input file\n");return -5;}
	
	char valid[numblock];
	for (lcv = 0;lcv < numblock; lcv++) {valid[lcv] = FALSE;}
	
	int tag[numblock];
	
	//Read defined input file until end of file is met
	
	while (!feof(memory)) {
		int charnum = 0;
		int ch = fgetc(memory);
		for (ch; ch != EOF && ch != '\n'; ch = fgetc(memory)) {
			if (ch != ' ' && ch != '	') {
				address[charnum++] = ch;
			}
		}
		if (charnum > 0) {
			address[charnum] = '\0';
			if (address[1] == 'x' || address[1] == 'X') {
				hexadd = address;
				decadd = strtoul(address, NULL, 0);
			}
			else {
				decadd = strtoul(address, NULL, 10);
			}
			
			//Parse memory address values
			
			caoffset = decadd % (2 << bitoffset - 1);
			caindex = (decadd >> bitoffset) % (2 << bitindex -1);
			catag = (decadd >> bitoffset + bitindex) % (2<< bittag - 1);
			
			//Define required formulas and print data
			
			if (!valid[caindex]) {
				valid[caindex] = TRUE;
				tag[caindex] = catag;
				totalmiss++;
				isHit = FALSE;
				isValid = FALSE;
			}
			else{
				isValid = TRUE;
				if (tag[caindex] == catag) {
					totalhit++;
					isHit = TRUE;
				}
				else if(tag[caindex] != catag){
					totalmiss++;
					cache_tag = tag[caindex];
					tag[caindex] = catag;
					isHit = FALSE;
				}
			}
			totalaccess = totalhit + totalmiss;
			missratio = (1 - (double)totalhit / (double)totalaccess);
			
			if (isTrace && isValid) {
				printf("%8x %8x %8x %8x %4s %7u %7u %7u %.08f\n", decadd, catag, caindex,
					   cache_tag, (isHit ? "Hit" : "Miss"), totalhit, totalmiss, totalaccess, missratio);
			}
			else if(isTrace){
				printf("%8x %8x %8x %8s %4s %7u %7u %7u %.08f\n", decadd, catag, caindex, "", (isHit ? "Hit" : "Miss"), totalhit, totalmiss, totalaccess, missratio);
			}
		}
	}
	fclose(memory);
	printf("Author: Cody Mercer\n");
	for (lcv = 1; lcv < ARGC; lcv++) {
		printf("%s ", argv[lcv]);
	}
	printf("\n");
	printf("# of hits: %u\n", totalhit);
	printf("# of misses: %u\n", totalmiss);
	printf("miss ratio: %.08f\n", missratio);
	return 0;
}
