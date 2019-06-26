(function process(/*RESTAPIRequest*/ request, /*RESTAPIResponse*/ response) {

    // Get incident number from URL
	var rPath = request.pathParams.INCnumber;
	var body = request.body.data;
	
	//used to add attachments
	var StringUtil = GlideStringUtil;
	var att = new Attachment();
	
	// query incident number
	var gr = new GlideRecord('incident');
	gr.addQuery('number', rPath);
	gr.query();
	if(gr.next()){

	// update incident 
		gr.short_description = body.short_description;
		gr.description = body.description;
		gr.state = getState(body.state);
		gr.impact = setPriority(body.priority);
		gr.urgency = setUrgency(body.priority);
		gr.assignment_group.setDisplayValue(body.assignment_group);
		gr.work_notes = body.work_notes;
		gr.comments = body.comments;
		gr.close_code = body.close_code;
		gr.close_notes = body.close_notes;
        gr.update();
        
		//add attachments
		if(body.u_attachment_data != ''){
		att.write('incident',gr.sys_id, body.u_filename, '', StringUtil.base64DecodeAsBytes(body.u_attachment_data));
	}
		
    }
    //Send Response to requester
	var incResponse = {
			Status: 200,
			number: gr.getDisplayValue('number'),
			Action: 'Updated'
			
		};
return incResponse;


})(request, response);




function setPriority(acsPriority)
	{
		if (acsPriority == '1'){
			return 1;
		}
		else if (acsPriority == '2'){
			return 2;
		}
		else if (acsPriority == '3'){
			return 3;
		}
			
		else
			{
			return 2;
			}
		
	}


function getState(acsState)
{
	if (acsState == 'New')
		return 1;
	else if (acsState == 'Active' || acsState == 'Work In Progress')	
		return 2;
	else if (acsState == 'Awaiting User Info')
		return 4;
	else if (acsState == 'Awaiting Vendor Info')
		return 9;
	else if (acsState == 'Awaiting Problem')
		return 3;
	else if (acsState == 'Awaiting Change')
		return 14;
	else
		return 6;
}

function setUrgency(acsDefaultUrgency){
	if(acsDefaultUrgency <= 3)
		return 1;
	else
		return 3;
}
