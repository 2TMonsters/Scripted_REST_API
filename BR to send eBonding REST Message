(function executeRule(current, previous /*null when async*/) {
	
	var keeper = current.sys_id;
	var newWorkNotes = ''; 
	var closeNotes = '';
    var closeCode = '';

    //get current work notes
	if(current.work_notes.changes()){
		newWorkNotes = current.work_notes.getJournalEntry(1);
    }
    //resolve ebonding incident
	if(current.state.changesTo(6 || 7)){ 
		closeCode = current.close_code;
		closeNotes = current.getValue('close_notes');
	}
	var r = new sn_ws.RESTMessageV2('eBonding Host', 'Post');
	// set variables that will be used in the request body
	r.setStringParameterNoEscape('ci', ''+current.getDisplayValue('cmdb_ci')+'');
	r.setStringParameterNoEscape('shDesc', ''+JSON.stringify(current.getValue('short_description'))+'');
	r.setStringParameterNoEscape('Category', ''+current.getDisplayValue('category') +'');
	r.setStringParameterNoEscape('AssignGr', ''+current.getDisplayValue('assignment_group')+'');
	r.setStringParameterNoEscape('Caller', ''+current.getDisplayValue('caller_id')+'');
	r.setStringParameterNoEscape('description', ''+JSON.stringify(current.getValue('description'))+'');
	r.setStringParameterNoEscape('priority', ''+current.priority+'');
	r.setStringParameterNoEscape('number', ''+current.number+'');
	r.setStringParameterNoEscape('state', ''+current.getDisplayValue('state')+'');
	r.setStringParameterNoEscape('wkNotes', ''+JSON.stringify(newWorkNotes)+'');
	r.setStringParameterNoEscape('clNotes', ''+JSON.stringify(closeNotes)+'');
    r.setStringParameterNoEscape('clCode', closeCode);
    
	//Build request body
	r.setRequestBody("{\"short_description\":\${shDesc},\"description\":${description},\"u_categorization\":\"${Category}\",\"assignment_group\":\"${AssignGr}\",\"work_notes\":${wkNotes},\"priority\":\"${priority}\",\"number\":\"${number}\",\"state\":\"${state}\",\"close_notes\":${clNotes},\"close_code\":\"${clCode}\",\"cmdb_ci\":\"${ci}\"}");
		
		var response = r.execute();
		var responseBody = response.getBody();
		var parsed = JSON.parse(responseBody);
        var httpStatus = response.getStatusCode();

        //Update incident with eBonding incident number	
		var gr = new GlideRecord('incident');
		gr.addQuery('sys_id', keeper);
		gr.query();
		if(gr.next()) {
			gr.u_integration = 'eBonding Host';
			gr.u_integration_id = parsed.ID;
			gr.update();
        }
        //Get attachments
	if(current.hasAttachments()){
			var n = 0;
			var att = new GlideRecord('sys_attachment');
			att.addQuery('table_name', 'incident');
			att.addQuery('table_sys_id',keeper);
			att.query();
			var b64attachment = '';
			while(att.next()){
				n++;
				var acsNumber = parsed.ID;
				var sa = new GlideSysAttachment();
				var binData = sa.getBytes(att);
                b64attachment = GlideStringUtil.base64Encode(binData);


                //Send attachments
				var attr = new sn_ws.RESTMessageV2('eBonding Host', 'Post');
				attr.setStringParameterNoEscape('attachMe', b64attachment);
				attr.setStringParameterNoEscape('attachMe_name', ''+att.getValue('file_name')+'');
				attr.setStringParameterNoEscape('ACSNum', acsNumber);
				attr.setRequestBody("{\"u_reference\":\"${ACSNum}\",\"attachment_name\":\"${attachMe_name}\",\"attachment\":\"${attachMe}\"}");
					var attresponse = attr.execute();
					var attresponseBody = attresponse.getBody();
					var attparsed = JSON.parse(attresponseBody);
					var atthttpStatus = attresponse.getStatusCode();
					
				}
			}
			
		})(current, previous);
