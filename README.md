# Lyfboat Code Structure
This is Lyfboat code structure document.
**IMS:** Inquiry Management System

[TBD]

# Overview

TBD

## Main Code Folder Structure

 - app/ : Build using inhouse framework and uses wordpress as a underlying layer.
- api/ (Consist Logic for API) : Build using Slim Framework
- root : Frontend Site (Essentially wordpress)

## IMS

Within app folder, you'll find bunch of folders which are explained below

- [assets](https://bitbucket.org/black-box/lyf/src/master/app/assets/) : Consist of all the css, js, fonts assets in this folder.
- [config](https://bitbucket.org/black-box/lyf/src/master/app/config/) : Consist config files
- [images](https://bitbucket.org/black-box/lyf/src/master/app/images/) : Consist of default image assets used by the system
- [include](https://bitbucket.org/black-box/lyf/src/master/app/include/)
- [lib](https://bitbucket.org/black-box/lyf/src/master/app/lib/) : All models are defined in this folder
-- funcitonal files : eg- [actions.function.php](https://bitbucket.org/black-box/lyf/src/master/app/lib/actions.function.php), [inquiry.function.php](https://bitbucket.org/black-box/lyf/src/master/app/lib/inquiry.function.php),
-- models : This folder has all the functional logic of the IMS application. This essentially act as Model in this framework
---- Model Classes (Files) eg- [Inquiry.php](https://bitbucket.org/black-box/lyf/src/master/app/lib/models/Inquiry.php), [Hospital.php](https://bitbucket.org/black-box/lyf/src/master/app/lib/models/Hospital.php),  [InquiryResponse.php](https://bitbucket.org/black-box/lyf/src/master/app/lib/models/InquiryResponse.php)
---- Elastic (Folder)
-------- ES Model Classes eg: [ElasticInquiry.php](https://bitbucket.org/black-box/lyf/src/master/app/lib/models/Elastic/ElasticInquiry.php), [ElasticMember.php](https://bitbucket.org/black-box/lyf/src/master/app/lib/models/Elastic/ElasticMember.php), [ElasticEmail.php](https://bitbucket.org/black-box/lyf/src/master/app/lib/models/Elastic/ElasticEmail.php)
- [modules](https://bitbucket.org/black-box/lyf/src/master/app/modules/) : Every module has its own set of tpl, php, js and ajax files. Which essentially act as view and controller
-- modulename eg: [inquiry](https://bitbucket.org/black-box/lyf/src/master/app/modules/inquiry/)
---- module.php eg: [view.received.inquiry.hcf.php](https://bitbucket.org/black-box/lyf/src/master/app/modules/inquiry/view.received.inquiry.hcf.php)
---- module.tpl eg: [view.received.inquiry.hcf.tpl](https://bitbucket.org/black-box/lyf/src/master/app/modules/inquiry/view.received.inquiry.hcf.tpl)
---- module.js eg: [view.received.inquiry.hcf.js](https://bitbucket.org/black-box/lyf/src/master/app/modules/inquiry/view.received.inquiry.hcf.js)
---- module.ajax.php eg: [view.received.inquiry.hcf.ajax.php](https://bitbucket.org/black-box/lyf/src/master/app/modules/inquiry/view.received.inquiry.hcf.ajax.php)
- [template](https://bitbucket.org/black-box/lyf/src/master/app/template/) : Has system and email templates
- [uploads](https://bitbucket.org/black-box/lyf/src/master/app/uploads/) : Should be part of assets. Being used by old functionality
- [widgets](https://bitbucket.org/black-box/lyf/src/master/app/widgets/) :

All your files and folders are presented as a tree in the file explorer. You can switch from one to another by clicking a file in the tree.

## How to register a module and 
[Register a Module](https://bitbucket.org/black-box/lyf/src/master/app/include/register-modules.php)
Open [register-modules.php](https://bitbucket.org/black-box/lyf/src/master/app/include/register-modules.php) and use below stub to define a module
	
```php
	//register_module( $title, $capability, $slug , $sidebar =  false)
	// register_submodule( $parent_slug, $title, $capability, $slug, $multiple_account_allowed =  false )
    register_module('Dashboard', 'dashboard', 'dashboard');
	register_submodule('dashboard', 'Dashboard', 'dashboard', 'dashboard', true);
```

## How to Define a menu page

Open [pages.php](https://bitbucket.org/black-box/lyf/src/master/app/include/pages.php) and register an array in `get_lb_pages()`. Eg:

```php
    array(
	    array(
	        "page_title" => "Manage Member",
	        "page_capability" => "member",
	        "page_url" => "?m=member&a=list.members",
	    ),

	    array(
	        "page_title" => "Media",
	        "page_capability" => "gallery",
	        "page_url" => "",
	        "page_children" => array(
	            array(
	                "page_title" => "Hospital Media",
	                "page_capability" => "hospital_gallery",
	                "page_url" => "?m=gallery&a=list.hospitals",
	            ),
	            array(
	                "page_title" => "Doctor Media",
	                "page_capability" => "doctor_gallery",
	                "page_url" => "?m=gallery&a=list.doctors",
	            ),
	        ),
	    )
	);
```
## Function Files
These are files with set of functions grouped by feature such as [hospital.function.php](https://bitbucket.org/black-box/lyf/src/master/app/lib/hospital.function.php), [inquiry.function.php](https://bitbucket.org/black-box/lyf/src/master/app/lib/inquiry.function.php), [member.function.php](https://bitbucket.org/black-box/lyf/src/master/app/lib/member.function.php), [treatment.function.php](https://bitbucket.org/black-box/lyf/src/master/app/lib/treatment.function.php) etc.
Ideally, All feature should use Model classes and not these functions. But good percentage of feature still uses old functions. Altough we have moved underlying logic in these functions to model classes.
## Models
This essentially control the CRUD operation over a Type of Object. For eg: [Inquiry.php](https://bitbucket.org/black-box/lyf/src/master/app/lib/models/Inquiry.php)
```php
<?php
namespace Wplib;
use AppElastic\ElasticInquiry;
use AppElastic\ElasticInquiryResponse;
use Wplib\AssignInquiry;
use Wplib\InquiryMeta;
use Wplib\Activity;
use Wplib\AppObject;
use Wplib\Tasks;

class Inquiry extends AppObject {	

	public $inquiry_id = -1;
	public $inquiry_name;
	public $member_id;
	public $hospital_id;
	public $hospital_name;
	...
	
	function __construct( $result = null ) {

		if ( is_array( $result ) ) {
			$this->inquiry_id 			 = ( isset( $result['inquiry_id'] ) ) ? $result['inquiry_id'] : -1;
			$this->member_id 			 = ( isset( $result['member_id'] ) ) ? $result['member_id'] : '';
			$this->hospital_id 			 = ( isset( $result['hospital_id'] ) ) ? $result['hospital_id'] : '0';
			$this->hcf_id 				 = ( isset( $result['hcf_id'] ) ) ? $result['hcf_id'] : '';
			...
		}
	}

	public static function find( $inquiry_id ) {
		global $wpdb;
		$sql 		= "SELECT *,
					(SELECT country_name FROM `lb_countries` where country_id = lb_inquiry.country) as patient_country_name
					FROM `lb_inquiry` WHERE inquiry_id = %d";
		
		$sql 		= $wpdb->prepare( $sql, $inquiry_id );
		$inquiry 	= $wpdb->get_row( $sql, ARRAY_A );
		if ( empty( $inquiry ) ) {
			return null;
		}
		$object = new self( $inquiry );
		return $object;

	}

	public static function find_by ( $args = array(), $multiple_result = true, $order_by = 'inquiry_id', $order = 'ASC', $limit = -1, $offset = 0 ) {
		
		global $wpdb;
		$objects 	= array();
		$sql 		= "SELECT * FROM `lb_inquiry`";
		$defaults 	=  array
						'inquiry_id' 				=> '%d',
						'member_id' 				=> '%d',
						'hospital_id' 				=> '%d',
						...
					);
		$sql .= AppQuery::parse_args( $defaults, $args, $order_by, $order, $limit, $offset );
		
		if ( $multiple_result ) {
			$results = $wpdb->get_results( $sql, ARRAY_A );
		} else {
			$results = $wpdb->get_row( $sql, ARRAY_A );	
		}
		
		if ( empty( $results ) ) {
			return null;
		} elseif ( $multiple_result ) {
			foreach ($results as $key => $value) {
				$objects[] = new self( $value );
			}
		} else {
			$objects = new self( $results );	
		}
		return $objects;
	}

	public function set_as_prospect( $new_treatment_id = '') {
		...
	}

	public function set_as_confirmed( $flow_id, $followers = array(), $note = '') {
		...
	}

	static public function add_note( $inquiry_id, $note, $user_type, $user_module_id, $status = 'Update' ) {
		...
	}

	private function update( $update_arr, $where_arr, $format = null, $where_format = null ) {
		global $wpdb;
		$return = $wpdb->update( 'lb_inquiry', $update_arr, $where_arr, $format, $where_format );
		return $return;
	}

	private function insert( $insert_arr, $format = '' ) {
		global $wpdb;
		$insert = $wpdb->insert( 'lb_inquiry', $insert_arr, $format );

		if ($insert) {
			return $wpdb->insert_id;
		} else {
			return $insert;
		}
	}

	public function save($skip_feed = false) {
		$inquiry_id 			= $this->inquiry_id;
		$member_id 				= $this->member_id;
		$hospital_id 			= $this->hospital_id;
		$hcf_id 				= $this->hcf_id;
		...

		$data 	=  array(
					'member_id' 			=> $member_id,
					'hospital_id' 			=> $hospital_id,
					'hcf_id' 				=> $hcf_id,
					...
					);
		
		$format 		= array(
						'%d', //member_id
						'%d', //hospital_id
                        '%d', //hcf_id
                        ...
						);
		if ( '-1' == $inquiry_id ) {
			$inquiry_id = $this->insert( $data, $format );
			$result 	= $inquiry_id;
		} else {
			$where_arr 	=  array(
				'inquiry_id' 	=> $inquiry_id,
			);
			
			$where_format 	= array(
				'%d',
			);
			
			$result = $this->update( $data, $where_arr, $format, $where_format );
		}
		return $result;
	}

}
```

## [Register an ElasticSearch Model](https://bitbucket.org/black-box/lyf/src/master/app/lib/models/Elastic/)
```php
<?php

namespace AppElastic;
use Elasticsearch\ClientBuilder;
use Wplib\Tag;
use Wplib\Inquiry;

/**
* 
*/
class ElasticInquiry {

	public $inquiry_id = -1;
	public $ref_id;
	public $member_id;
    public $member_name;
    ...

	function __construct( $result = null ) {

		if ( is_array( $result ) ) {
			$this->inquiry_id 				= intval($result['inquiry_id']);
			$this->ref_id 					= $result['ref_id'];
			$this->member_id 				= intval($result['member_id']);
			$this->member_name 				= ( !empty( $result['member_name'] ) ) ? $result['member_name'] : '';
			...
		}
	}

	private function delete_mapping() {
		global $es_client;
		$params = [
			'index' => ES_INDEX_INQUIRY,
			'type' => 'detail',
		];

		// Delete doc at /my_index/my_type
		$response = $es_client->delete($params);
	}
	
	private function set_mapping() {

		global $es_client;
		
		// Set the index
		$params['index'] = ES_INDEX_INQUIRY;
		try {
			$ret = $es_client->indices()->getSettings($params);
		} catch (\Exception $e) {
			$es_client->indices()->create($params);
		}

		// Set the type
		$params['index'] = ES_INDEX_INQUIRY;
		$params['type']  = 'detail';
		$params['update_all_types'] = true;

		// Adding a new type to an existing index
		$inquiry_mapping = array(
			'_source' => array(
				'enabled' => true
			),
			'properties' => array(
				'inquiry_id' => array(
					'type' => 'long',
				),
				'ref_id' => array(
					'type' => 'string',
				),
				'member_id' => array(
					'type' => 'long',
				),
				'member_name' => array(
					'type' => 'string',
				),
				...
			)
		);

		$params['body']['detail'] = $inquiry_mapping;

		// Update the index mapping
		try {
			$response = $es_client->indices()->putMapping( $params );
		} catch (\Exception $e) {
			$response = array();
		}
		return $response;
	}

	static function get_by_inquiry_id ( $inquiry_id ) {
		global $es_client;
		$params = [
			'index' => ES_INDEX_INQUIRY,
			'type' => 'detail',
			'id' => $inquiry_id
		];

		try {
			$response = $es_client->get($params);
		} catch (\Exception $e) {
			$response = array();
		}
		return $response;
	}

	public function delete_inquiry_by_id( $inquiry_id ) {
		
		global $es_client;
		$deleteParams = [
			'index' => ES_INDEX_INQUIRY,
			'type' => 'detail',
			'id' => $inquiry_id,
		];

		try {
			$response = $es_client->delete($deleteParams);
		} catch (\Exception $e) {
			$response = "Error in deleting inquiry wth ID: $inquiry_id";
		}

		return $response;
	}

	public function feed_inquiry ( $inquiry_id ) {

		$inquiry 	= (array) Inquiry::find( $inquiry_id );
		$imetas 	= (array) InquiryMeta::get_by_inquiry_id( $inquiry_id );
		$tasks 		= array();
		if ( $inquiry['forwarded_inquiry_id'] == 0 ) {
			$tasks 		= Tasks::get_inquiry_tasks_by_active_state($inquiry['inquiry_id'], $inquiry['process_state']);
		}		
		return $this->_feed_using_data( $inquiry, $imetas, $tasks );
	}

	public function feed_all_inquiry ( $set_mapping = false ) {
		
		ini_set('max_execution_time', 0);
		global $es_client;

		if ( $set_mapping ) {
		  $this->set_mapping();
		}

		$args = array(
			'order_by' => 'inquiry_id',
			'order' => 'DESC',
		);
		$inquiries = (array) Inquiry::get_all($args);

		$objects = array();
		$inquiry_count = 0;
		$inquiry_params = array(
			"body" => array()
		);
		
		/**
		 * Hashing Code
		 */
		$hashed_data = array();
		/** Get All Treatments */
			$treatment_args = array( "treatment_status" => array("active", "deactive") );
			$treatments = Treatment::find_by( $treatment_args);
			$hashed_data['treatments'] = array();
			foreach ($treatments as $key => $value) {
				$hashed_data['treatments'][$value->treatment_id] = $value;
			}

		/** Get All Hospitals */
			$hospital_args = array( "hospital_status" => array("active", "deactive") );
			$hospitals = Hospital::find_by( $hospital_args, $multiple_result = true, $order_by = 'hospital_id', $order = 'ASC', $limit = -1 );
			$hashed_data['hospitals'] = array();
			foreach ($hospitals as $key => $value) {
				$hashed_data['hospitals'][$value->hospital_id] = $value;
			}

		/** Get All Doctors */
			$doctor_args = array( "status" => array("active", "deactive") );
			$doctors = get_doctors($doctor_args);
			$hashed_data['doctors'] = array();
			foreach ($doctors as $key => $value) {
				$hashed_data['doctors'][$value->doctor_id] = $value;
			}

		/** Get All Members */
			$member_args = array( "status" => array("active") );
			$members = get_members($member_args);
			$hashed_data['members'] = array();
			foreach ($members as $key => $value) {
				$hashed_data['members'][$value->id] = $value;
			}

		/** Get All Members */
			$countries = get_countries();
			$hashed_data['countries'] = array();
			foreach ($countries as $key => $value) {
				$hashed_data['countries'][$value->country_id] = $value;
			}

		/** Get All HCFs */
			$hcf_args = array( "status" => array("active") );
			$hcfs = get_hcfs($hcf_args);
			$hashed_data['hcfs'] = array();
			foreach ($hcfs as $key => $value) {
				$hashed_data['hcfs'][$value->hcf_id] = $value;
			}
		
		foreach ($inquiries as $inquiry) {
						
			if ( !empty( $inquiry ) ) {
				
				$inquiry_count++;
				$imetas = (array) InquiryMeta::get_by_inquiry_id( $inquiry->inquiry_id );
				$inquiry_data = $this->_process_inquiry_data($inquiry, $imetas, $hashed_data);
				$data['process_state'] = $inquiry->process_state;
				$tasks_res 	= ElasticTask::get_by_inquiry_id($inquiry->inquiry_id, $data);
				if ( $tasks_res['total'] > 0 ) {
					$tasks = $tasks_res['data'];
					$inquiry_data['tasks'] = $tasks;
				}
				$inquiry_data = new self( $inquiry_data );
				if ( !empty($tasks) ) {
					$object['tasks'] = $tasks;
				}
				$inquiry_params['body'][] = array(
					
					"index" => array(
						"_index" 	=> ES_INDEX_INQUIRY,
						"_type" 	=> 'detail',
						"_id" 		=> $inquiry->inquiry_id,
					),
				);
				$inquiry_params['body'][] = $inquiry_data;

				if ( $inquiry_count % 100 == 0 ) {
					$responses = $es_client->bulk($inquiry_params);
					// erase the old bulk request
					$inquiry_params = array(
						"body" => array()
					);
		
					// unset the bulk response when you are done to save memory
					unset($responses);
				}
			}

		}

		// Send the last batch if it exists
		if (!empty($inquiry_params['body'])) {
			$responses = $es_client->bulk($inquiry_params);
			
		}

		return $inquiry_count;

	}

	static function search_by_tags ( $tags ) {

		global $es_client;

		$params = [
			'index' => ES_INDEX_INQUIRY,
			'type' => 'detail',
			'body' => [
				'query' => [
					'match' => [
						'tags' => json_encode($tags)
					]
				]
			]
		];

		$response = $es_client->search($params);
		return $response;

	}

	static public function filter( $filters, $limit = 10, $offset = 0 ) {
		
		global $es_client;

		$params = [
			'index' => ES_INDEX_INQUIRY,
			'type' => 'detail',
			'size' => 50,
			'body' => [
			]
		];

		if ( !empty( $filters ) ) {

			$query = array(
				"query" => array(
					"bool" => array(
						"filter" => array(),
					),
				),
			);

			foreach ($filters as $filter_key => $value ) {
				$filter_value = ( is_array( $value ) ) ? json_encode( $value ) : $value ;
				
				$match = array(
					"match" => array(
						$filter_key => $filter_value,
					),
				);

				array_push($query["query"]["bool"]["filter"], $match);
			}
		}


		$json = json_encode($query);
		$params['body'] = $json;

		try {
			$searched_data = $es_client->search($params);

			$inquiry_data = array();

			foreach ($searched_data['hits']['hits'] as $value) {
				array_push($inquiry_data, $value['_source']);
			}

			$response = array(
				'total' => $searched_data['hits']['total'],
				'data' => $inquiry_data,
			);

		} catch (\Exception $e) {

			$response = array(
				"total" => 0,
				"data" => array()
			);
		}

		return $response;
	}

	private function _process_inquiry_data ( $inquiry, $imetas, $hashed_data = array() ) {

		global $es_client;

		if ( !is_array($inquiry)) {
			$inquiry = (array) $inquiry;
		}

		if ( !is_array($imetas)) {
			$imetas = (array) $imetas;
		}

		if ( !empty($hashed_data) ) {
			$inquiry = $this->_process_bulk_data( $inquiry, $imetas, $hashed_data );
		} else {
			$inquiry = $this->_process_single_data( $inquiry, $imetas );
		}

		$inquiry['process_state'] = intval($inquiry['process_state']);
		
		if ( !empty($inquiry['process_state']) && $inquiry['process_state'] > 0 ) {
			$stateData = ProcessFlow::get_process_state( $inquiry['process_state'] );
			if ( !empty($stateData) ) {
				$inquiry['process_state_name'] = $stateData->process_name;
				$inquiry['process_stage'] = $stateData->process_child_of;
				$inquiry['process_stage_name'] = $stateData->stage->process_name;
			}
		}

		$inquiry['ref_id'] = get_ref_id_by_inquiry_id( $inquiry['inquiry_id'], $inquiry['member_id'], $inquiry['treatment_id']);
		$inquiry['patient_name'] = ucwords($inquiry['patient_first_name']." ".$inquiry['patient_last_name']);

		if ( !empty($inquiry['custom_treatment_name']) ) {
			$inquiry['treatment_name'] .= ' - ' . $inquiry['custom_treatment_name'];
		}

		$inquiry['inquiry_custom_date'] = date('d M Y', strtotime($inquiry['inquiry_date']));

		/* Attachment Flag */
		$attachments 		= new MemberAttachment();
		// $inq_attachments 	= $attachments->find_by_member_and_treatment( $inquiry['member_id'], $inquiry['treatment_id'], array("status" => array('new','approved','default','rejected')));
		$attachment_inquiry = ($inquiry['forwarded_inquiry_id'] != 0) ? $inquiry['forwarded_inquiry_id'] : $inquiry['inquiry_id'];
		$inq_attachments 	= $attachments->find_by_member_and_inquiry( $inquiry['member_id'], $attachment_inquiry, array( "attachment_status" => array('new','approved','default','rejected') ));
		if ( !empty($inq_attachments) ) {
			$inquiry['has_attachments'] = "yes";
		} else {
			$inquiry['has_attachments'] = "no";
		}

		/* Member Email Flag */
		$email_response = AppEmails::is_member_email_valid( $inquiry['member_id'] );
		$inquiry['valid_member_email'] = isset($email_response['switch']) ? $email_response['switch'] : false;

		/*Get group conversation data*/
		$conversation 		= new Conversation();
		$conversation_args 	= array( "status" => array('active') );
		$group_data 		= $conversation->get_conversation_group($inquiry['inquiry_id'], $conversation_args);
		$hcf_user_id 		= app_get_user_id($inquiry['hcf_id'], 'hcf');

		if ( $group_data && $hcf_user_id ) {
			$conversation_data = $conversation->get_conversation_messages($group_data->conversation_group_id, $hcf_user_id);
		} else {
			$conversation_data = '';
		}

		$inquiry['msg_count'] = !empty($conversation_data) ? count($conversation_data) : '';

		$last_msg = $conversation->get_last_message_by_inquiry( $inquiry['inquiry_id']);
		$is_last_msg_read_by_member = false;

		if ( !empty($last_msg) ) {

			$message_array = array();

			array_push($message_array, $last_msg);
			$group_id = $group_data->conversation_group_id; 

			$updated = $conversation->process_conversation_markers( $message_array, $group_id, $hcf_user_id);
			
			if ( !empty($updated) ) {
				$msg_data = (array) $updated[0];
				$key = array_search('member', array_column($msg_data['seen'], 'user_type'));
				if ( $key !== false ) {
					$is_last_msg_read_by_member = true;
				}
			}
			
			$user_role = app_get_user_role($last_msg->source_user_id);

			if ( $user_role == 'member' ) {
				$inquiry['last_message'] = "Received";
			} else {
				$inquiry['last_message'] = "Sent";
			}

		}

		/* Include Inquiry Notes */
		if ( !empty($inquiry['note']) ) {
			
			$notes = json_decode($inquiry['note']);

			$total_notes = count($notes);
			$last_note = $notes[$total_notes-1];

			foreach ($notes as $note) {
				$user_id = app_get_user_id( $note->note_provider_id, $note->note_provider_type );
				$note->note_provider_name = app_get_user_module_name($user_id, $note->note_provider_type);
				$note->image = app_get_user_logo($note->note_provider_id, $note->note_provider_type);
			}

			$inquiry['last_note_on'] = $last_note->note_date;

			$notes = array_reverse($notes);
			$inquiry['note'] = json_encode($notes);
			$inquiry['notes'] = json_encode($notes);
			$inquiry['last_note'] = json_encode($notes[0]);

			$report_notes = array();
			foreach ( $notes as $note ) {
				$format = $note->note_content . ' ['. $note->note_date.']';
				array_push($report_notes, $format);
			}
			$inquiry['report_notes'] = implode(' <br/> ', $report_notes);

		}

		/* Include Assignment Data */
		$assignment_status 		= array( 'status' => array( "assigned") );
		$assignment_data 		= get_inquiry_assignee( $inquiry['inquiry_id'], $assignment_status );

		$inquiry['assigned_to'] = !empty($assignment_data) ? $assignment_data->user_id : 'unassigned';
		if (!empty($assignment_data)) {
			$assignment_data->assigned_on = ($assignment_data->assigned_on != '0000-00-00 00:00:00') ? strtotime($assignment_data->assigned_on) : NULL;
			$assignment_data->last_updated = ($assignment_data->last_updated != '0000-00-00 00:00:00') ? strtotime($assignment_data->last_updated) : NULL;
		}
		
		$inquiry['assigned_data'] = $assignment_data;

		/* Include Follower Data */
		$inquiry_followers_data = AssignInquiry::get_followers( $inquiry['inquiry_id'] );
		$org_inquiry_followers_data = $inquiry_followers_data;
		if (!empty($inquiry_followers_data)) {
			foreach ($inquiry_followers_data as $flwr) {
				$flwr->assigned_on = $flwr->assigned_on != '0000-00-00 00:00:00' ? strtotime($flwr->assigned_on) : NULL;
				$flwr->last_updated = $flwr->last_updated != '0000-00-00 00:00:00' ? strtotime($flwr->last_updated) : NULL;
				if ($flwr->last_updated === false) {
					$flwr->last_updated = null;
				}
			}
		}
		$inquiry['inquiry_followers_data'] = $inquiry_followers_data;

		if ( !empty($inquiry_followers_data) ) {
			$follower_array = array();
			foreach ($inquiry_followers_data as $value) {
				array_push($follower_array, $value->assigned_to);
			}
			$inquiry['inquiry_followers'] = $follower_array;
		}

		/* Include Inquiry Date formatted */
		$inquiry['inquiry_custom_date'] 	= date('d M Y', strtotime($inquiry['inquiry_date']));
		$inquiry['filter_inquiry_date'] 	= date('Y-m-d H:i:s', strtotime($inquiry['inquiry_date']));

		/* Include Inquiry Custom Status */
		if ( $inquiry['inquiry_status'] == 'new' ) {
			$inquiry['inquiry_custom_status'] = 'New Inquiry';
		} 
		elseif ( $inquiry['inquiry_status'] == 'delete' || $inquiry['inquiry_status'] == 'archive') {
			$inquiry['inquiry_custom_status'] = ucwords($inquiry['inquiry_status']).'d';
		}
		elseif ( $inquiry['inquiry_status'] == 'discard') {
			$inquiry['inquiry_custom_status'] = ucwords($inquiry['inquiry_status']).'ed';
		} 
		else {
			$inquiry['inquiry_custom_status'] = ucwords(str_replace('-', ' ', $inquiry['inquiry_status']));
		}

		/* Child Inquiry Statues, Hospital Ids and Doctor Ids for responded child inquires*/
		$child_args = array(
			"status" => array(
				"pending",
				"responded",
				"accepted",
				"rejected",
				"discard",
				"closed",
			),
		);
		$child_inquiries = get_all_child_inquiries($inquiry['inquiry_id'], $child_args);
		$inquiry['forwarded_to'] = count($child_inquiries);

		$child_status = array();
		$hospital_ids = array();
		$doctor_ids = array();
		$child_ref_ids = array();

		if ( !empty($child_inquiries) ) {

			foreach ($child_inquiries as $child ) {
				
				if ( !in_array($child->inquiry_status, $child_status) ) {
					$child_status[] = $child->inquiry_status;
				}

				if ( $child->inquiry_status == "responded" || $child->inquiry_status == "accepted" || $child->inquiry_status == "rejected" ) {

					if ( !in_array($child->hospital_id, $hospital_ids) ) {
						$hospital_ids[] = $child->hospital_id;
					}
					if ( !in_array($child->doctor_id, $doctor_ids) ) {
						$doctor_ids[] = $child->doctor_id;
					}
					
				}

				
				array_push($child_ref_ids, $child->ref_id);
			}

			if ( !in_array("responded", $child_status) && !in_array("accepted", $child_status) && !in_array("rejected", $child_status) ) {
				array_push($child_status, "no_response");
			}

		}
		// printPre($child_status, '$child_status');
		$inquiry['child_status'] = $child_status;
		$inquiry['child_ref_ids'] = $child_ref_ids;

		/* Get Hospital Location for Responded Child Inquiry */
		$hospital_locations = array();
		if ( !empty($hospital_ids) ) {
			foreach ($hospital_ids as $hospital) {
				if ( !empty($hospital) ) {
					$hospital_data = get_hospital($hospital);
					array_push($hospital_locations, clean_slug($hospital_data->country, '_'));
				}
			}
		}

		/* Get Doctor Location for Responded Child Inquiry */
		$doctor_locations = array();
		if ( !empty($doctor_ids) ) {
			foreach ($doctor_ids as $doctor) {
				if ( !empty($doctor) ) {
					$doctor_data = get_doctor($doctor);
					array_push($doctor_locations, clean_slug($doctor_data->country, '_'));
				}
			}
		}

		$all_locations = array_merge($hospital_locations, $doctor_locations);
		$all_locations = array_values(array_unique($all_locations));

		$inquiry['responded_prov_location'] = $all_locations;

		$inquiry['tag_names'] = array();

		if ( !empty( $imetas ) ) {
			foreach ( $imetas as $meta ) {
				$meta_key	 			= $meta->meta_key;
				$meta_value	 			= $meta->meta_value;

				if ( $meta_key == 'tags' && !empty($meta_value) ) {

					$inquiry['tags'] = json_decode($meta_value);
					$inquiry['tag_names'] = array();
					$previous_tags = json_decode($meta_value);
					foreach ($previous_tags as $value ) {
						$tag_data = Tag::get_by_tag_slug( $value );
						if ( !empty($tag_data) ) {
							$tag = array();
							$tag['slug'] = $tag_data->tag_slug;
							$tag['name'] = $tag_data->tag_name;
							$tag['color'] = $tag_data->tag_color;
							array_push($inquiry['tag_names'], (object) $tag);
						}
					}
				
				} elseif ( $meta_key == 'closed' ) {

					$closedData = json_decode($meta_value);
					$data = array();
					$data['state'] = $closedData->state;
					$data['fields'] = json_encode($closedData->fields);
					$inquiry["closed_data"] = (object) $data;

				} else {
					$inquiry[$meta_key] = $meta_value;
				}
			}
		}
		
		return $inquiry;
	}

	private function _process_bulk_data ( $inquiry, $imetas, $hashed_data = array() ) {

		global $es_client;

		if ( !is_array($inquiry)) {
			$inquiry = (array) $inquiry;
		}

		if ( !is_array($imetas)) {
			$imetas = (array) $imetas;
		}

		$hashed_treatments	= isset($hashed_data['treatments']) && !empty($hashed_data['treatments']) ? $hashed_data['treatments'] : '';
		$hashed_hospitals	= isset($hashed_data['hospitals']) && !empty($hashed_data['hospitals']) ? $hashed_data['hospitals'] : '';
		$hashed_doctors		= isset($hashed_data['doctors']) && !empty($hashed_data['doctors']) ? $hashed_data['doctors'] : '';
		$hashed_members		= isset($hashed_data['members']) && !empty($hashed_data['members']) ? $hashed_data['members'] : '';
		$hashed_countries	= isset($hashed_data['countries']) && !empty($hashed_data['countries']) ? $hashed_data['countries'] : '';
		$hashed_hcfs		= isset($hashed_data['hcfs']) && !empty($hashed_data['hcfs']) ? $hashed_data['hcfs'] : '';

		$inquiry['doctor_id'] 				= 0;
		$inquiry['treatment_name']			= '';
		$inquiry['hospital_name']			= '';
		$inquiry['member_name']				= '';
		$inquiry['member_phone_number']		= '';
		$inquiry['member_email']			= '';
		$inquiry['doctor_name']				= '';
		$inquiry['patient_country_name']	= '';
		$inquiry['hcf_name']				= '';

		/* Include Treatment Name */
		if ( !empty($hashed_treatments) ) {
			if ( !empty( $hashed_treatments[ $inquiry['treatment_id'] ] ) ) {
				$inquiry['treatment_name'] = $hashed_treatments[ $inquiry['treatment_id'] ]->treatment_name;
			}
		}

		/* Include hospital Name */
		if ( !empty($hashed_hospitals) ) {
			if ( !empty( $hashed_hospitals[ $inquiry['hospital_id'] ] ) ) {
				$inquiry['hospital_name'] = $hashed_hospitals[ $inquiry['hospital_id'] ]->hospital_name;
			}
		} 

		/* Include Member Name */
		if ( !empty($hashed_members) ) {
			if (isset($inquiry['member_id']) && isset($hashed_members[$inquiry['member_id']])) {
				$member_data = $hashed_members[$inquiry['member_id']];
				if ( !empty($member_data) && isset($member_data->firstname) ) {
					$inquiry['member_name'] = ucwords( $member_data->firstname. " " .$member_data->lastname );
					$inquiry['member_phone_number'] = $member_data->telephone_country_code . " " .$member_data->telephone;
					$inquiry['member_email'] = $member_data->email;
				}
			}
		}

		/* Include Doctor Name */
		if ( !empty($hashed_doctors) ) {
			if ( !empty( $hashed_doctors[ $inquiry['doctor_id'] ] ) ) {
				$inquiry['doctor_name'] = 'Dr. '. $hashed_doctors[ $inquiry['doctor_id'] ]->first_name.' '.$hashed_doctors[ $inquiry['doctor_id'] ]->last_name;
			}
		}

		/* Include Patient Country */
		if ( !empty($hashed_countries) ) {
			if ( !empty( $hashed_countries[ $inquiry['country'] ] ) ) {
				$inquiry['patient_country_name'] = $hashed_countries[ $inquiry['country'] ]->country_name;
			}
		}

		/* Include HCF Name */
		if ( !empty($hashed_hcfs) ) {
			if ( !empty( $hashed_hcfs[ $inquiry['hcf_id'] ] ) ) {
				$inquiry['hcf_name'] = $hashed_hcfs[ $inquiry['hcf_id'] ]['hcf_name'];
			}
		}

		/* Get doctor data from lb_doctor_inquiry table */
		$doctor = get_doctor_by_inquiry_id( $inquiry['inquiry_id'] );
		if ( !empty($doctor) ) {
			$doctor_data = $hashed_doctors[$doctor->doctor_id];
			if ( !empty($doctor_data) ) {
				$inquiry['doctor_id'] = $doctor->doctor_id;
				$inquiry['doctor_name'] = 'Dr. '. $doctor_data->first_name.' '.$doctor_data->last_name;
			}
		}

		return $inquiry;
	}

	private function _process_single_data ( $inquiry, $imetas ) {

		global $es_client;

		if ( !is_array($inquiry)) {
			$inquiry = (array) $inquiry;
		}

		if ( !is_array($imetas)) {
			$imetas = (array) $imetas;
		}

		$inquiry['treatment_name']			= '';
		$inquiry['hospital_name']			= '';
		$inquiry['member_name']				= '';
		$inquiry['member_phone_number']		= '';
		$inquiry['member_email']			= '';
		$inquiry['doctor_name']				= '';
		$inquiry['doctor_id'] 				= 0;
		$inquiry['patient_country_name']	= '';

		/* Include Treatment Name */
		$treatment = get_treatment( $inquiry['treatment_id'] );
		if ( !empty($treatment) ) {
			$inquiry['treatment_name'] = $treatment->treatment_name;
		}

		/* Include hospital Name */
		$hospital = get_hospital( $inquiry['hospital_id'] );
		if ( !empty($hospital) ) {
			$inquiry['hospital_name'] = $hospital->hospital_name;
		}

		/* Include Member Name */
		$member_data = get_member( $inquiry['member_id'] );
		if ( !empty($member_data) && isset($member_data->firstname) ) {
			$inquiry['member_name'] = ucwords( $member_data->firstname. " " .$member_data->lastname );
			$inquiry['member_phone_number'] = $member_data->telephone_country_code . " " .$member_data->telephone;
			$inquiry['member_email'] = $member_data->email;
		}

		/* Include Doctor Name */
		$doctor = get_doctor( $inquiry['doctor_id'] );
		if ( !empty($doctor) ) {
			$inquiry['doctor_name'] = 'Dr. '. $doctor->first_name.' '.$doctor->last_name;
		}

		/* Get doctor data from lb_doctor_inquiry table */
		$doctor = get_doctor_by_inquiry_id( $inquiry['inquiry_id'] );
		if ( !empty($doctor) ) {	
			$doctor_data = get_doctor( $doctor->doctor_id );
			if ( !empty($doctor_data) ) {
				$inquiry['doctor_id'] = $doctor->doctor_id;
				$inquiry['doctor_name'] = 'Dr. '. $doctor_data->first_name.' '.$doctor_data->last_name;
			}
		}

		/* Include Patient Country Name */
		$country_data = get_country($inquiry['country']);
		if ( !empty($country_data) ) {
			$inquiry['patient_country_name'] = $country_data->country_name;
		}

		/* Get Hospital Location for Responded Child Inquiry */
		$hospital_locations = array();
		// @FIX : $hospital_ids does not exist
		// if ( !empty($hospital_ids) ) {
		// 	foreach ($hospital_ids as $hospital) {
		// 		if ( !empty($hospital) ) {
		// 			$hospital_data = get_hospital($hospital);
		// 			array_push($hospital_locations, clean_slug($hospital_data->country, '_'));
		// 		}
		// 	}
		// }

		/* Get Doctor Location for Responded Child Inquiry */
		$doctor_locations = array();
		// @FIX : $doctor_ids does not exist
		// if ( !empty($doctor_ids) ) {
		// 	foreach ($doctor_ids as $doctor) {
		// 		if ( !empty($doctor) ) {
		// 			$doctor_data = get_doctor($doctor);
		// 			array_push($doctor_locations, clean_slug($doctor_data->country, '_'));
		// 		}
		// 	}
		// }

		$all_locations = array_merge($hospital_locations, $doctor_locations);
		$all_locations = array_values(array_unique($all_locations));

		$inquiry['responded_prov_location'] = $all_locations;
		
		return $inquiry;
	}

	private function _feed_using_data ( $inquiry, $imetas, $tasks = array() ) {
		global $es_client;
		$inquiry = $this->_process_inquiry_data($inquiry, $imetas);
		$object = new self( $inquiry );
		$object = (array) $object;
		if ( !empty($tasks) ) {
			$object['tasks'] = $tasks;
		}
		$active_count = 0;
		foreach ($tasks as $task) {
			$active_count = ($task['task_status'] == 'open') ? $active_count + 1 : $active_count;
		}
		$object['task_count'] = count($tasks);
		$object['active_task_count'] = $active_count;
		$params = [
			'index' => ES_INDEX_INQUIRY,
			'type' => 'detail',
			'id' => $inquiry['inquiry_id'],
			'body' => $object,
		];
		try {
			$response = $es_client->index($params);
		} catch (\Exception $e) {
			$response = "Error in feeding inquiry wth ID: " . $inquiry['inquiry_id'];
		}

		return $response;
	}

	/**
	** 
	** $filters = array(
	**	"inquiry_status" => array("new", "forwarded");
	**	"forwarded_inquiry_id" => 123 (int variable);
	** );

	*/
	static public function search( $search_string, $filters, $limit = 25, $offset = 0, $sort_by = 'inquiry_id', $sort_order = 'desc', $custom_fields = array(), $fuzzy_flag = false ) {
		
		global $es_client;

		$params = [
			'index' => ES_INDEX_INQUIRY,
			'type' => 'detail',
			'size' => $limit,
			'from' => $offset,
			'body' => "",
		];

		$query = array();

		if ( !empty($custom_fields) ) {
			$fields = $custom_fields;
		} else {
			$fields = array(
				'ref_id',
				'member_name',
				'member_phone_number',
				'member_email',
				'custom_treatment_name',
				'treatment_name',
				'patient_name',
				'patient_country_name',
				'child_ref_ids',
				'nationality',
				'process_state_name',
				'inquiry_source',
				'source',
				'device_category',
				'medium',
			);
		}

		if ( $search_string != '' ) {

			$search_string = trim($search_string);
			$words = explode(" ", $search_string);

			/**
			 * Updated Search Code
			 */
				$must_query = array();
				$should_query = array(
					array(
						"multi_match" => array(
							"fields" => $fields,
							"query" => $search_string,
							"operator" => "and",
							"boost" => 1
						),
					),
				);

				if ( $fuzzy_flag ) {
					$must_query[] = array(
						'multi_match' => array(
							"query" => $search_string,
							"fields" => $fields,
							"fuzziness" => "auto",
						),
					);
					$should_query = array(
						"multi_match" => array(
							"fields" => $fields,
							"query" => $search_string,
							"type" => "phrase_prefix",
							"boost" => 10
						),
					);
				} else {
					$must_query[] = array(
						"multi_match" => array(
							"fields" => $fields,
							"query" => $search_string,
							"type" => "phrase_prefix",
							"boost" => 10
						),
					);
					$should_query[] = array(
						'multi_match' => array(
							"query" => $search_string,
							"fields" => $fields,
							"fuzziness" => "auto",
						),
					);
				}

				$query["query"]["bool"]["must"][] = $must_query;
				$query["query"]["bool"]["should"][] = $should_query;
		}

		if ( !empty( $filters ) ) {

			foreach ($filters as $filter_key => $value ) {

				if ( $filter_key == 'OR_CONDITION' ) {
					
					foreach ($filters[$filter_key] as $Or_key => $Or_value) {
						
						if ( is_array( $Or_value ) ) {
							
							$query["query"]["bool"]['filter']['bool']['should'][] = array( 'terms' =>  array( $Or_key => $Or_value ) );
						
						} else {
						
							$query["query"]["bool"]['filter']['bool']['should'][] = array( 'term' =>  array( $Or_key => $Or_value ) );
						
						}

					}

				} else {

					if ( is_array( $value ) ) {
						if ( $filter_key == 'DATE_RANGE' ) {

							$query["query"]["bool"]['filter']['bool']['must'][]['range'] = array(
								"filter_inquiry_date" => array(
									"gte" => $value["from"],
									"lte" => $value["to"],
								),
							);

						} else if( $filter_key == 'RANGE' ) {
							$data = array();
							(isset($value["gt"])) ? $data[$value["key"]]['gt'] = $value["gt"] : '';
							(isset($value["gte"])) ? $data[$value["key"]]['gte'] = $value["gte"] : '';
							(isset($value["lt"])) ? $data[$value["key"]]['lt'] = $value["lt"] : '';
							(isset($value["lte"])) ? $data[$value["key"]]['lte'] = $value["lte"] : '';
							$query["query"]["bool"]['filter']['bool']['must'][]['range'] = $data;
						} else if( isset($value['type']) && $value['type'] == 'nested' ) {
							$data = array();
							$data['nested']['path'] = $filter_key;
							$nestedfields = $value['fields'];
							$nested_data = array();
							foreach ($nestedfields as $nested_key => $nested_value) {
								if (is_array($nested_value)) {
									if ( isset($nested_value['type']) && $nested_value['type'] == 'range' ) {
										$nested_data[] = array( 'range' =>  array( $nested_key => $nested_value['filter'] ) );
									} else {
										$nested_data[] = array( 'terms' =>  array( $nested_key => $nested_value ) );
									}
								} else {
									$nested_data[] = array( 'term' =>  array( $nested_key => $nested_value ) );
								}
							}
							$data['nested']['query']['bool']['must'] = $nested_data;
							$query["query"]["bool"]['filter']['bool']['must'][] = $data;
						} else {

							$query["query"]["bool"]['filter']['bool']['must'][] = array( 'terms' =>  array( $filter_key => $value ) );
							
						}
			
					} else {
			
						$query["query"]["bool"]['filter']['bool']['must'][] = array( 'term' =>  array( $filter_key => $value ) );

					}

				}

			}

		}

		// $final_query['query']['filtered'] = $query;
		
		/**
		 * Sort only applies when searched term is null
		 */
		// if ($sort_by == "inquiry_status" || $sort_by == "status") {
		// 	$sort_by = $sort_by.".keyword";
		// }
		if ( empty($search_string) && !empty($sort_by) ) {
			if ( $sort_order == 'custom' && is_array($sort_by) ) {
				$query['sort'] = $sort_by;
			} else {
				$sort_by = !is_array($sort_by) ? (array) $sort_by : $sort_by;
				foreach ($sort_by as $sort) {
					$query['sort'] = array(
						$sort => array(
							'order' => $sort_order,
						)
					);
				}
			}
		}
		// printPre($query, '$query');
		$json = json_encode($query);
		$params['body'] = $json;
		
		try {
			$searched_data 	= $es_client->search($params);
			$inquiry_data 	= array();

			foreach ($searched_data['hits']['hits'] as $value) {
				array_push($inquiry_data, $value['_source']);
			}

			$response = array(
				'total' => $searched_data['hits']['total'],
				'data' => $inquiry_data,
			);

		} catch (\Exception $e) {

			$response = array(
				"total" => 0,
				"data" => array()
			);
		}

		return $response;

	}

}
```

## [Modules](https://bitbucket.org/black-box/lyf/src/master/app/modules/)
IMS is categorised in modules and sub modules format. Each submodule has its own set of tpl, js, php, ajax files. Which essentially control what will be visible on the sub module page and how data interaction will happen.
An implementation using Inquiry View is explained below:
#### Eg: [Inquiry Module](https://bitbucket.org/black-box/lyf/src/master/app/modules/inquiry/)

 - [view.received.inquiry.hcf.tpl](https://bitbucket.org/black-box/lyf/src/master/app/modules/inquiry/view.received.inquiry.hcf.tpl)
 - [view.received.inquiry.hcf.php](https://bitbucket.org/black-box/lyf/src/master/app/modules/inquiry/view.received.inquiry.hcf.php)
 - [view.received.inquiry.hcf.js](https://bitbucket.org/black-box/lyf/src/master/app/modules/inquiry/view.received.inquiry.hcf.js)
 - [view.received.inquiry.hcf.ajax.php](https://bitbucket.org/black-box/lyf/src/master/app/modules/inquiry/view.received.inquiry.hcf.ajax.php)
Every sub module will consist these 4 files.
