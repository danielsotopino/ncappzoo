
NCCOMPILE = mo.py

GREEN = '\033[1;32m'
YELLOW = '\033[1;33m'
RED = '\033[1;31m'
NOCOLOR = '\033[0m'

MODEL_BASE_NAME = tiny-yolo-v1_53000

PROTOTXT_FILENAME= tiny-yolo-v1.prototxt
GET_PROTOTXT = wget --no-cache -P . http://ncs-forum-uploads.s3.amazonaws.com/ncappzoo/tiny_yolo/${PROTOTXT_FILENAME}

CAFFEMODEL_FILENAME = tiny-yolo-v1_53000.caffemodel
GET_CAFFEMODEL = wget --no-cache -P . -N http://ncs-forum-uploads.s3.amazonaws.com/ncappzoo/tiny_yolo/${CAFFEMODEL_FILENAME}

IR = tiny-yolo-v1_53000.xml
IMG = ../../data/images/nps_chair.png

.PHONY: all
all: data deps compile_model

NETWORK_NAME = tiny_yolo_v1

.PHONY: deps
deps: prototxt caffemodel
	@echo $(YELLOW)'\n'${NETWORK_NAME}": Making dependencies..."$(NOCOLOR)

	
.PHONY: data
data:
	@echo $(YELLOW)'\n'${NETWORK_NAME}": Downloading required data... No data needed."$(NOCOLOR)


.PHONY: prototxt
prototxt: 
	@echo $(YELLOW)'\n'${NETWORK_NAME}": Downloading prototxt file..."$(NOCOLOR)
	@if [ -e ${PROTOTXT_FILENAME} ] ; \
	then \
		echo " - Prototxt file already exists"; \
	else \
		echo "Downloading Prototxt file"; \
		${GET_PROTOTXT}; \
		if [ -e ${PROTOTXT_FILENAME} ] ; \
		then \
			echo " - Got prototext file." ; \
		else \
			echo "***\nError - Could not download prototxt file. Check network and proxy settings \n***\n"; \
			exit 1; \
		fi ; \
	fi 

.PHONY: caffemodel
caffemodel: 
	@echo $(YELLOW)'\n'${NETWORK_NAME}": Downloading caffemodel file..."$(NOCOLOR)
	@if [ -e ${CAFFEMODEL_FILENAME} ] ; \
	then \
		echo " - Caffemodel file already exists"; \
	else \
		echo " - Downloading caffemodel file"; \
		${GET_CAFFEMODEL}; \
		if ! [ -e ${CAFFEMODEL_FILENAME} ] ; \
		then \
			echo "***\nError - Could not download caffemodel file. Check network and proxy settings \n***\n"; \
			exit 1; \
		fi ; \
	fi  
	
	
.PHONY: compile_model
compile_model: prototxt caffemodel
	@echo $(YELLOW)'\n'${NETWORK_NAME}": Compiling model to IR..."$(NOCOLOR)
	@if [ -z "$(INTEL_OPENVINO_DIR)" ] ; \
	then \
		echo "Please initiate the Intel OpenVINO environment by going to the installation directory for openvino and running the setupvars.sh file in the bin folder." ; \
		exit 1 ; \
	else \
		echo "Intel OpenVINO environment is already set!" ; \
	fi
	@if [ -e ${MODEL_BASE_NAME}.xml ] && [ -e ${MODEL_BASE_NAME}.bin ] ; \
	then \
		echo " - IR files exist. No need to compile." ; \
	else \
	    ${NCCOMPILE} --input_model=${CAFFEMODEL_FILENAME} --input_proto=${PROTOTXT_FILENAME} --data_type=FP16 --scale_values=[255] --reverse_input_channels; \
	fi
	

.PHONY: run_py
run_py: data deps compile_model
	@echo $(YELLOW)'\n'${NETWORK_NAME}": Running the python sample..."$(NOCOLOR)
	python3 ./tiny_yolo_v1.py --image=${IMG} --ir=${IR}


.PHONY: run
run: run_py
	@echo $(YELLOW)'\n'${NETWORK_NAME}": Running the python sample..."$(NOCOLOR)


.PHONY: install-reqs
install-reqs: 
	@echo $(YELLOW)'\n'${NETWORK_NAME}": Checking application requirements...\n"$(NOCOLOR)
	@echo "No requirements needed."
	

.PHONY: uninstall-reqs
uninstall-reqs: 
	@echo $(YELLOW)'\n'${NETWORK_NAME}": Uninstalling requirements..."$(NOCOLOR)
	@echo "Nothing to uninstall."
	
	
.PHONY: help
help:
	@echo "\nPossible make targets: ";
	@echo $(YELLOW)"  make help "$(NOCOLOR)"- shows this message";
	@echo $(YELLOW)"  make run or run_py "$(NOCOLOR)"- runs the run.py python example program";
	@echo $(YELLOW)"  make all "$(NOCOLOR)"- makes the following: data deps compile_model";
	@echo $(YELLOW)"  make compile_model "$(NOCOLOR)"- runs Model Optimizer compiler tool to compile an IR file for the network";
	@echo $(YELLOW)"  make data "$(NOCOLOR)"- Downloads the required data.";
	@echo $(YELLOW)"  make deps "$(NOCOLOR)"- Makes dependencies for project, downloads and prepares model etc.";
	@echo $(YELLOW)"  make install-reqs "$(NOCOLOR)"- Installs requirements needed to run this sample on your system.";
	@echo $(YELLOW)"  make uninstall-reqs "$(NOCOLOR)"- Uninstalls requirements that were installed by the sample program.";
	@echo $(YELLOW)"  make clean "$(NOCOLOR)"- removes all created content\n"
	@echo " ----- "$(YELLOW)"Model information"$(NOCOLOR)" -----"
	@echo $(YELLOW)" Description: "$(NOCOLOR)
	@echo "  This network does object detection on 20 different classes.";
	@echo $(YELLOW)" Model inputs:"$(NOCOLOR)
	@echo "  name: 'data', shape: [1x3x448x448], Expected color order is BGR after optimization.";
	@echo $(YELLOW)" Model outputs:"$(NOCOLOR)
	@echo "  name: 'fc9', shape: [1, 1470]. See README for more output information.\n"


clean_caffe_model:
	@echo $(YELLOW)'\n'${NETWORK_NAME}": making clean_caffe_model..."$(NOCOLOR)
	rm -f ${PROTOTXT_FILENAME}
	rm -f ${CAFFEMODEL_FILENAME}


clean: clean_caffe_model
	@echo $(YELLOW)'\n'${NETWORK_NAME}": making clean..."$(NOCOLOR)
	rm -f ${MODEL_BASE_NAME}.xml
	rm -f ${MODEL_BASE_NAME}.bin
	rm -f ${MODEL_BASE_NAME}.mapping
	

