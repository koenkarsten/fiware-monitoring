UNITTESTS_PROGS				= suite_argument_parser \
					  suite_broker_common \
					  suite_broker_fiware \
					  suite_broker_xifi

UNITTESTS_NAGIOS_MAIN			= nagios_main.c

UNITTESTS_NAGIOS_MOCKS			= find_host \
					  find_service \
					  find_command \
					  grab_host_macros_r \
					  grab_service_macros_r \
					  get_raw_command_line_r \
					  process_macros_r

UNITTESTS_CURL_EASY_MOCKS		= curl_easy_init \
					  curl_easy_setopt \
					  curl_easy_perform \
					  curl_easy_cleanup \
					  curl_easy_strerror

UNITTESTS_BROKER_COMMON_MOCKS		= gethostname \
					  gethostbyname \
					  neb_set_module_info \
					  neb_register_callback \
					  curl_global_init \
					  curl_global_cleanup

UNITTESTS_BROKER_MINIMAL_MOCKS		= gethostname \
					  gethostbyname \
					  $(UNITTESTS_NAGIOS_MOCKS)

UNITTESTS_BROKER_ALL_MOCKS		= $(UNITTESTS_BROKER_COMMON_MOCKS) \
					  $(UNITTESTS_NAGIOS_MOCKS) \
					  $(UNITTESTS_CURL_EASY_MOCKS)

noinst_PROGRAMS				= $(UNITTESTS_PROGS)
check_PROGRAMS				= $(UNITTESTS_PROGS)
TESTS					= $(check_PROGRAMS)

NAGIOS_BASE_OBJECTS			= $(wildcard @NAGIOS_SRCDIR@/base/*.o)
NAGIOS_COMMON_OBJECTS			= $(wildcard @NAGIOS_SRCDIR@/common/*.o)
NAGIOS_OBJECTS				= $(filter-out %/nagios.o, $(NAGIOS_BASE_OBJECTS) $(NAGIOS_COMMON_OBJECTS))
NAGIOS_LIBS				= $(shell awk '/^(MATH|PERL|SOCKET|THREAD|BROKER)LIBS/ {sub(/.*=/,""); print}' \
					  @NAGIOS_SRCDIR@/base/Makefile)

suite_argument_parser_SOURCES		= suite_argument_parser.cc
suite_argument_parser_CXXFLAGS		= -Wall @CPPUNIT_CFLAGS@
suite_argument_parser_LDADD		= @CPPUNIT_LIBS@ \
					  $(top_builddir)/src/ngsi_event_broker_fiware_la-argument_parser.lo

suite_broker_common_SOURCES		= suite_broker_common.cc
nodist_suite_broker_common_SOURCES	= $(UNITTESTS_NAGIOS_MAIN)
suite_broker_common_CXXFLAGS		= -fpermissive -w @CPPUNIT_CFLAGS@ \
					  $(foreach FN,$(UNITTESTS_BROKER_COMMON_MOCKS),-Wl,--wrap,$(FN))
suite_broker_common_LDADD		= -lcurl $(NAGIOS_LIBS) @CPPUNIT_LIBS@ \
					  $(top_builddir)/src/ngsi_event_broker_fiware_la-ngsi_event_broker_common.lo \
					  $(top_builddir)/src/ngsi_event_broker_fiware_la-argument_parser.lo \
					  $(NAGIOS_OBJECTS)

suite_broker_fiware_SOURCES		= suite_broker_fiware.cc
nodist_suite_broker_fiware_SOURCES	= $(UNITTESTS_NAGIOS_MAIN)
suite_broker_fiware_CXXFLAGS		= -fpermissive -w @CPPUNIT_CFLAGS@ \
					  $(foreach FN,$(UNITTESTS_BROKER_ALL_MOCKS),-Wl,--wrap,$(FN))
suite_broker_fiware_LDADD		= -lcurl $(NAGIOS_LIBS) @CPPUNIT_LIBS@ \
					  $(top_builddir)/src/ngsi_event_broker_fiware_la-ngsi_event_broker_common.lo \
					  $(top_builddir)/src/ngsi_event_broker_fiware_la-ngsi_event_broker_fiware.lo \
					  $(top_builddir)/src/ngsi_event_broker_fiware_la-argument_parser.lo \
					  $(NAGIOS_OBJECTS)

suite_broker_xifi_SOURCES		= suite_broker_xifi.cc
nodist_suite_broker_xifi_SOURCES	= $(UNITTESTS_NAGIOS_MAIN)
suite_broker_xifi_CXXFLAGS		= -fpermissive -w @CPPUNIT_CFLAGS@ \
					  $(foreach FN,$(UNITTESTS_BROKER_MINIMAL_MOCKS),-Wl,--wrap,$(FN))
suite_broker_xifi_LDADD			= -lcurl $(NAGIOS_LIBS) @CPPUNIT_LIBS@ \
					  $(top_builddir)/src/ngsi_event_broker_xifi_la-ngsi_event_broker_common.lo \
					  $(top_builddir)/src/ngsi_event_broker_xifi_la-ngsi_event_broker_xifi.lo \
					  $(top_builddir)/src/ngsi_event_broker_xifi_la-argument_parser.lo \
					  $(NAGIOS_OBJECTS)

$(UNITTESTS_NAGIOS_MAIN): @NAGIOS_SRCDIR@/base/nagios.c
	$(SED) 's/\(int[ \t]*\)main\([ \t]*(\)/\1nagios_main\2/' $< > $@

# remove gcov and cppunit files
clean-local:
	rm -f *.gcda *.gcno *-cppunit-results.xml
