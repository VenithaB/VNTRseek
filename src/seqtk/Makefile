CC=/home/vb/miniconda3/envs/vntr_env/bin/x86_64-conda_cos6-linux-gnu-gcc
CFLAGS=-g -Wall -O2 -Wno-unused-function -I/home/vb/miniconda3/envs/vntr_env/include
LDFLAGS=-L/home/vb/miniconda3/envs/vntr_env/lib
all:seqtk
seqtk:seqtk.c khash.h kseq.h
	$(CC) $(CFLAGS) seqtk.c -o $@ $(LDFLAGS) -lz -lm
clean:
	rm -fr gmon.out *.o seqtk
