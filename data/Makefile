BIN_PATH=../build
TRIALS = 1 2 3
PCAP_BASE =zoom-5min-3users-two-devices-stressed-screenshare-trial
# Target directory structure
EXP_DIR =zoom-5min-3users-two-devices-stressed-screenshare-trials

all: $(foreach t,$(TRIALS),$(EXP_DIR)/trial$(t)/stats.csv)

# Pattern rule to process trials
$(EXP_DIR)/trial%/stats.csv: $(PCAP_BASE)%.pcap
	@mkdir -p $(EXP_DIR)/trial$*
	@echo "--- Processing Trial $* ---"
	
	# Step 1: Run Cabernet Zoom flows
	$(BIN_PATH)/zoom_flows -i $< \
		-f flows.csv -t types.csv -r rate.csv -z $(PCAP_BASE)$*.zpkt
	
	# Step 2: Run Cabernet Zoom RTP
	$(BIN_PATH)/zoom_rtp -i $(PCAP_BASE)$*.zpkt \
		-p pkts.csv -s streams.csv -f frames.csv -t stats.csv
	
	# Step 3: Run Cabernet Zoom Meetings
	$(BIN_PATH)/zoom_meetings -i $(PCAP_BASE)$*.zpkt \
		-m meetings.csv -u unique.csv

	# Step 4: Render HTML reports
	-Rscript -e 'library(rmarkdown); rmarkdown::render("stats.Rmd")'
	-Rscript -e 'library(rmarkdown); rmarkdown::render("streams.Rmd")'

	# Step 5: Move generated .csv and .html files into the trial folder
	# (Excluded .zpkt from moving)
	-mv *.csv $(EXP_DIR)/trial$*/
	-mv *.html $(EXP_DIR)/trial$*/
	
	# Optional: Delete the large .zpkt file once the trial is done to save space
	$(RM) $(PCAP_BASE)$*.zpkt

clean:
	$(RM) -r $(EXP_DIR)
	$(RM) *.csv *.zpkt *.html

.PHONY: all clean
