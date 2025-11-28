TASK_NAME = $(shell basename $(CURDIR))

TASK_OUT = kol

OBJ = ./obj
VER = ./$(TASK_NAME)

FLAGS = -std=c++11 -g -Wall -Werror -pedantic -fno-elide-constructors

all: $(OBJ) test

$(OBJ):
	mkdir -p $(OBJ)

test :
	make --always-make -s test_for_60
	make --always-make -s test_for_80
	make --always-make -s test_for_100

test_for_60  : set_vars_for_60  build execute check_mem accept
test_for_80  : set_vars_for_80  build execute check_mem accept
test_for_100 : set_vars_for_100 build execute check_mem accept

set_vars_for_60 :
	$(eval GRADE := 60)

set_vars_for_80 :
	$(eval GRADE := 80)

set_vars_for_100 :
	$(eval GRADE := 100)

TASK_LEVEL = TASK for $(GRADE)%

build : $(TASK_OUT)
	echo "Compilation of $(TASK_LEVEL)..."

$(TASK_OUT) : $(OBJ)/$(TASK_NAME).o
	g++ $(FLAGS) -DFOR_$(GRADE) $^ -o $@ || \
	( \
		echo "**************************************"; \
		echo "*     LINKING of $(TASK_LEVEL) FAILED      *"; \
		echo "**************************************"; \
		echo; \
		exit 1 \
	)


$(OBJ)/$(TASK_NAME).o : $(TASK_NAME)_main.cpp $(TASK_NAME).h
	g++ $(FLAGS) -DFOR_$(GRADE) -c  $< -o $@ || \
	( \
		echo "******************************************"; \
		echo "  COMPILATION of $(TASK_LEVEL) FAILED     "; \
		echo "******************************************"; \
		echo; \
		exit 1 \
	)

execute :
	echo "Basic test of $(TASK_LEVEL) ..."
	./$(TASK_OUT) || \
	( \
		echo "*****************************************"; \
		echo "   BASIC TEST of $(TASK_LEVEL) FAILED    "; \
		echo "*****************************************"; \
		echo; \
		exit 1 \
	)

check_mem :
	echo "Valgrind test of $(TASK_LEVEL)..."
	valgrind --leak-check=full --error-exitcode=1 ./$(TASK_OUT) || \
	( \
		echo "********************************************"; \
		echo "  VALGRIND TEST of $(TASK_LEVEL) FAILED     "; \
		echo "********************************************"; \
		echo; \
		exit 1 \
	)

accept :
	echo "************************************"
	echo "  TESTS of $(TASK_LEVEL) PASSED     "
	echo "************************************"
	echo

verify :
	# Creating subfolder with the same name
	mkdir -p $(VER)
	# Forcing relative symbolic links (use *_solved.c file)
	ln -frs $(TASK_NAME)_solved.h   $(VER)/$(TASK_NAME).h
	ln -frs $(TASK_NAME)_main.cpp   $(VER)/$(TASK_NAME)_main.cpp
	ln -frs makefile                $(VER)/makefile
	# Running make in subfolder
	make -C $(VER) all

diff :
	meld $(TASK_NAME).h $(TASK_NAME)_solved.h &

clean :
	-rm -rf $(TASK_OUT) $(OBJ) $(VER)
