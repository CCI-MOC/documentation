running=OS_STDOUT_CAPTURE=${OS_STDOUT_CAPTURE:-1} \
OS_STDERR_CAPTURE=${OS_STDERR_CAPTURE:-1} \
OS_TEST_TIMEOUT=${OS_TEST_TIMEOUT:-500} \
OS_TEST_LOCK_PATH=${OS_TEST_LOCK_PATH:-${TMPDIR:-'/tmp'}} \
${PYTHON:-python} -m subunit.run discover -t ${OS_TOP_LEVEL:-./} ${OS_TEST_PATH:-./tempest/test_discover} --list 
running=OS_STDOUT_CAPTURE=${OS_STDOUT_CAPTURE:-1} \
OS_STDERR_CAPTURE=${OS_STDERR_CAPTURE:-1} \
OS_TEST_TIMEOUT=${OS_TEST_TIMEOUT:-500} \
OS_TEST_LOCK_PATH=${OS_TEST_LOCK_PATH:-${TMPDIR:-'/tmp'}} \
${PYTHON:-python} -m subunit.run discover -t ${OS_TOP_LEVEL:-./} ${OS_TEST_PATH:-./tempest/test_discover}  --load-list /tmp/tmpYGe8_1
running=OS_STDOUT_CAPTURE=${OS_STDOUT_CAPTURE:-1} \
OS_STDERR_CAPTURE=${OS_STDERR_CAPTURE:-1} \
OS_TEST_TIMEOUT=${OS_TEST_TIMEOUT:-500} \
OS_TEST_LOCK_PATH=${OS_TEST_LOCK_PATH:-${TMPDIR:-'/tmp'}} \
${PYTHON:-python} -m subunit.run discover -t ${OS_TOP_LEVEL:-./} ${OS_TEST_PATH:-./tempest/test_discover}  --load-list /tmp/tmpvclWlW
running=OS_STDOUT_CAPTURE=${OS_STDOUT_CAPTURE:-1} \
OS_STDERR_CAPTURE=${OS_STDERR_CAPTURE:-1} \
OS_TEST_TIMEOUT=${OS_TEST_TIMEOUT:-500} \
OS_TEST_LOCK_PATH=${OS_TEST_LOCK_PATH:-${TMPDIR:-'/tmp'}} \
${PYTHON:-python} -m subunit.run discover -t ${OS_TOP_LEVEL:-./} ${OS_TEST_PATH:-./tempest/test_discover}  --load-list /tmp/tmpYCkY_b
running=OS_STDOUT_CAPTURE=${OS_STDOUT_CAPTURE:-1} \
OS_STDERR_CAPTURE=${OS_STDERR_CAPTURE:-1} \
OS_TEST_TIMEOUT=${OS_TEST_TIMEOUT:-500} \
OS_TEST_LOCK_PATH=${OS_TEST_LOCK_PATH:-${TMPDIR:-'/tmp'}} \
${PYTHON:-python} -m subunit.run discover -t ${OS_TOP_LEVEL:-./} ${OS_TEST_PATH:-./tempest/test_discover}  --load-list /tmp/tmpju4L0i
running=OS_STDOUT_CAPTURE=${OS_STDOUT_CAPTURE:-1} \
OS_STDERR_CAPTURE=${OS_STDERR_CAPTURE:-1} \
OS_TEST_TIMEOUT=${OS_TEST_TIMEOUT:-500} \
OS_TEST_LOCK_PATH=${OS_TEST_LOCK_PATH:-${TMPDIR:-'/tmp'}} \
${PYTHON:-python} -m subunit.run discover -t ${OS_TOP_LEVEL:-./} ${OS_TEST_PATH:-./tempest/test_discover}  --load-list /tmp/tmpY_zeJL
running=OS_STDOUT_CAPTURE=${OS_STDOUT_CAPTURE:-1} \
OS_STDERR_CAPTURE=${OS_STDERR_CAPTURE:-1} \
OS_TEST_TIMEOUT=${OS_TEST_TIMEOUT:-500} \
OS_TEST_LOCK_PATH=${OS_TEST_LOCK_PATH:-${TMPDIR:-'/tmp'}} \
${PYTHON:-python} -m subunit.run discover -t ${OS_TOP_LEVEL:-./} ${OS_TEST_PATH:-./tempest/test_discover}  --load-list /tmp/tmpFXhfu5
running=OS_STDOUT_CAPTURE=${OS_STDOUT_CAPTURE:-1} \
OS_STDERR_CAPTURE=${OS_STDERR_CAPTURE:-1} \
OS_TEST_TIMEOUT=${OS_TEST_TIMEOUT:-500} \
OS_TEST_LOCK_PATH=${OS_TEST_LOCK_PATH:-${TMPDIR:-'/tmp'}} \
${PYTHON:-python} -m subunit.run discover -t ${OS_TOP_LEVEL:-./} ${OS_TEST_PATH:-./tempest/test_discover}  --load-list /tmp/tmpYaDRNd
running=OS_STDOUT_CAPTURE=${OS_STDOUT_CAPTURE:-1} \
OS_STDERR_CAPTURE=${OS_STDERR_CAPTURE:-1} \
OS_TEST_TIMEOUT=${OS_TEST_TIMEOUT:-500} \
OS_TEST_LOCK_PATH=${OS_TEST_LOCK_PATH:-${TMPDIR:-'/tmp'}} \
${PYTHON:-python} -m subunit.run discover -t ${OS_TOP_LEVEL:-./} ${OS_TEST_PATH:-./tempest/test_discover}  --load-list /tmp/tmpvLGNXH
running=OS_STDOUT_CAPTURE=${OS_STDOUT_CAPTURE:-1} \
OS_STDERR_CAPTURE=${OS_STDERR_CAPTURE:-1} \
OS_TEST_TIMEOUT=${OS_TEST_TIMEOUT:-500} \
OS_TEST_LOCK_PATH=${OS_TEST_LOCK_PATH:-${TMPDIR:-'/tmp'}} \
${PYTHON:-python} -m subunit.run discover -t ${OS_TOP_LEVEL:-./} ${OS_TEST_PATH:-./tempest/test_discover}  --load-list /tmp/tmpOYslZd
running=OS_STDOUT_CAPTURE=${OS_STDOUT_CAPTURE:-1} \
OS_STDERR_CAPTURE=${OS_STDERR_CAPTURE:-1} \
OS_TEST_TIMEOUT=${OS_TEST_TIMEOUT:-500} \
OS_TEST_LOCK_PATH=${OS_TEST_LOCK_PATH:-${TMPDIR:-'/tmp'}} \
${PYTHON:-python} -m subunit.run discover -t ${OS_TOP_LEVEL:-./} ${OS_TEST_PATH:-./tempest/test_discover}  --load-list /tmp/tmpPERvC8
running=OS_STDOUT_CAPTURE=${OS_STDOUT_CAPTURE:-1} \
OS_STDERR_CAPTURE=${OS_STDERR_CAPTURE:-1} \
OS_TEST_TIMEOUT=${OS_TEST_TIMEOUT:-500} \
OS_TEST_LOCK_PATH=${OS_TEST_LOCK_PATH:-${TMPDIR:-'/tmp'}} \
${PYTHON:-python} -m subunit.run discover -t ${OS_TOP_LEVEL:-./} ${OS_TEST_PATH:-./tempest/test_discover}  --load-list /tmp/tmpgg_2_V
running=OS_STDOUT_CAPTURE=${OS_STDOUT_CAPTURE:-1} \
OS_STDERR_CAPTURE=${OS_STDERR_CAPTURE:-1} \
OS_TEST_TIMEOUT=${OS_TEST_TIMEOUT:-5tempest.api.image.v1.test_images_negative.CreateDeleteImagesNegativeTest
    test_delete_image_blank_id[id-04f72aa3-fcec-45a3-81a3-308ef7cc82bc,negative]OK  1.28
    test_delete_image_id_is_over_35_character_limit[id-a4a448ab-3db2-4d2d-b9b2-6a1271241dfe,negative]OK  1.89
    test_delete_image_negative_image_id[id-4ed757cd-450c-44b1-9fd1-c819748c650d,negative]OK  1.50
    test_delete_image_non_hex_string_id[id-950e5054-a3c7-4dee-ada5-e576f1087abd,negative]OK  0.03
    test_delete_image_with_invalid_image_id[id-bb016f15-0820-4f27-a92d-09b2f67d2488,negative]OK  2.03
tempest.api.image.admin.v2.test_images.BasicAdminOperationsImagesTest
    test_admin_deactivate_reactivate_image[id-951ebe01-969f-4ea9-9898-8a3f1f442ab0]SKIP  0.00
setUpClass (tempest.api.image.v1.test_images
    ListImagesTest)                                                   FAIL
tempest.api.image.v2.test_images.BasicOperationsImagesTest
    test_delete_image[id-f848bb94-1c6e-45a4-8726-39e3a5b23535,smoke]  OK  4.33
tempest.api.image.v1.test_image_members_negative.ImageMembersNegativeTest
    test_add_member_with_non_existing_image[id-147a9536-18e3-45da-91ea-b037a028f364,negative]OK  1.73
    test_delete_member_with_non_existing_image[id-e1559f05-b667-4f1b-a7af-518b52dc0c0f,negative]OK  1.63
tempest.api.image.v2.test_images_tags_negative.ImagesTagsNegativeTest
    test_delete_non_existing_tag[id-39c023a2-325a-433a-9eea-649bf1414b19,negative]OK  4.71
    test_update_tags_for_non_existing_image[id-8cd30f82-6f9a-4c6e-8034-c1b51fba43d9,negative]OK  0.03
tempest.api.image.v2.test_images_tags.ImagesTagsTest
    test_update_delete_tags_for_image[id-10407036-6059-4f95-a2cd-cbbbee7ed329]OK  3.82
tempest.api.image.v1.test_images.UpdateImageMetaTest
    test_list_image_metadata[id-01752c1c-0275-4de3-9e5b-876e44541928] OK  1.08
tempest.api.image.v2.test_images_member_negative.ImagesMemberNegativeTest
    test_image_share_invalid_status[id-b79efb37-820d-4cf0-b54c-308b00cf842c,negative]OK  4.60
tempest.api.image.v1.test_images.CreateRegisterImagesTest
    test_register_http_image[id-6d0e13a7-515b-460c-b91f-9f4793f09816] FAIL
    test_register_image_with_min_ram[id-05b19d55-140c-40d0-b36b-fafd774d421b]OK  5.21
    test_register_remote_image[id-69da74d9-68a9-404b-9664-ff7164ccb0f5]FAIL
tempest.api.image.v1.test_images_negative.CreateDeleteImagesNegativeTest
    test_delete_non_existent_image[id-ec652588-7e3c-4b67-a2f2-0fa96f57c8fc,negative]OK  3.30
    test_register_with_invalid_container_format[id-036ede36-6160-4463-8c01-c781eee6369d,negative]OK  0.01
    test_register_with_invalid_disk_format[id-993face5-921d-4e84-aabf-c1bba4234a67,negative]OK  1.19
tempest.api.image.v2.test_images.BasicOperationsImagesTest
    test_register_upload_get_image_file[id-139b765e-7f3d-4b3d-8b37-3ca3876ee318,smoke]OK  6.77
tempest.api.image.v2.test_images_negative.ImagesNegativeTest
    test_delete_image_null_id[id-32248db1-ab88-4821-9604-c7c369f1f88c,negative]OK  0.96
    test_delete_non_existing_image[id-6fe40f1c-57bd-4918-89cc-8500f850f3de,negative]OK  1.19
    test_get_delete_deleted_image[id-e57fc127-7ba0-4693-92d7-1d8a05ebcba9,negative]OK  5.03
    test_get_image_null_id[id-ef45000d-0a72-4781-866d-4cb7bf2562ad,negative]OK  0.97
    test_get_non_existent_image[id-668743d5-08ad-4480-b2b8-15da34f81d9f,negative]OK  0.97
    test_register_with_invalid_container_format[id-292bd310-369b-41c7-a7a3-10276ef76753,negative]OK  0.99
tempest.api.image.v1.test_images.UpdateImageMetaTest
    test_update_image_metadata[id-d6d7649c-08ce-440d-9ea7-e3dda552f33c]OK  6.99
tempest.api.image.v2.test_images.BasicOperationsImagesTest
    test_update_image[id-f66891a7-a35c-41a8-b590-a065c2a1caa6,smoke]  OK  5.04
tempest.api.image.v2.test_images_member_negative.ImagesMemberNegativeTest
    test_image_share_owner_cannot_accept[id-27002f74-109e-4a37-acd0-f91cd4597967,negative]OK  8.48
tempest.api.image.v1.test_images.CreateRegisterImagesTest
    test_register_then_upload[id-3027f8e6-3492-4a11-8575-c3293017af4d]OK  7.94
tempest.api.image.v2.test_images_negative.ImagesNegativeTest
    test_register_with_invalid_disk_format[id-70c6040c-5a97-4111-9e13-e73665264ce1,negative]OK  3.20
tempest.api.image.v1.test_image_members_negative.ImageMembersNegativeTest
    test_delete_member_with_non_existing_tenant[id-f5720333-dd69-4194-bb76-d2f048addd56,negative]OK  14.42
tempest.api.image.v2.test_images.ListImagesTest
    test_get_image_schema[id-622b925c-479f-4736-860d-adeaf13bc371]    OK  0.94
    test_get_images_schema[id-25c8d7b2-df21-460f-87ac-93130bcdc684]   OK  0.01
    test_index_no_params[id-1e341d7a-90a9-494c-b143-2cdf2aeb6aee]     OK  0.10
    test_list_images_param_container_format[id-9959ca1d-1aa7-4b7a-a1ea-0fff0499b37e]OK  1.35
    test_list_images_param_disk_format[id-4a4735a7-f22f-49b6-b0d9-66e1ef7453eb]OK  0.09
    test_list_images_param_limit[id-e914a891-3cc8-4b40-ad32-e0a39ffbddbb]OK  0.09
    test_list_images_param_min_max_size[id-4ad8c157-971a-4ba8-aa84-ed61154b1e7f]OK  1.52
    test_list_images_param_size[id-cf1b9a48-8340-480e-af7b-fe7e17690876]OK  0.10
    test_list_images_param_status[id-7fc9e369-0f58-4d05-9aa5-0969e2d59d15]OK  1.35
    test_list_images_param_visibility[id-7a95bb92-d99e-4b12-9718-7bc6ab73e6d2]OK  0.10
tempest.api.image.v1.test_image_members_negative.ImageMembersNegativeTest
    test_get_image_without_membership[id-f25f89e4-0b6c-453b-a853-1f80b9d7ef26,negative]OK  9.92
tempest.api.image.v1.test_image_members.ImageMembersTest
    test_add_image_member[id-1d6ef640-3a20-4c84-8710-d95828fdb6ad]    OK  26.51
    test_get_shared_images[id-6a5328a5-80e8-4b82-bd32-6c061f128da9]   OK  15.70
    test_remove_member[id-a76a3191-8948-4b44-a9d6-4053e5f2b138]       OK  5.98

Slowest 10 tests took 107.91 secs:
tempest.api.image.v1.test_image_members.ImageMembersTest
    test_add_image_member[id-1d6ef640-3a20-4c84-8710-d95828fdb6ad]        26.51
    test_get_shared_images[id-6a5328a5-80e8-4b82-bd32-6c061f128da9]       15.70
    test_remove_member[id-a76a3191-8948-4b44-a9d6-4053e5f2b138]           5.98
tempest.api.image.v1.test_image_members_negative.ImageMembersNegativeTest
    test_delete_member_with_non_existing_tenant[id-f5720333-dd69-4194-bb76-d2f048addd56,negative]  14.42
    test_get_image_without_membership[id-f25f89e4-0b6c-453b-a853-1f80b9d7ef26,negative]  9.92
tempest.api.image.v1.test_images.CreateRegisterImagesTest
    test_register_image_with_min_ram[id-05b19d55-140c-40d0-b36b-fafd774d421b]  5.21
    test_register_then_upload[id-3027f8e6-3492-4a11-8575-c3293017af4d]    7.94
tempest.api.image.v1.test_images.UpdateImageMetaTest
    test_update_image_metadata[id-d6d7649c-08ce-440d-9ea7-e3dda552f33c]   6.99
tempest.api.image.v2.test_images.BasicOperationsImagesTest
    test_register_upload_get_image_file[id-139b765e-7f3d-4b3d-8b37-3ca3876ee318,smoke]  6.77
tempest.api.image.v2.test_images_member_negative.ImagesMemberNegativeTest
    test_image_share_owner_cannot_accept[id-27002f74-109e-4a37-acd0-f91cd4597967,negative]  8.48

======================================================================
FAIL: tempest.api.image.v1.test_images.CreateRegisterImagesTest.test_register_http_image[id-6d0e13a7-515b-460c-b91f-9f4793f09816]
----------------------------------------------------------------------
Traceback (most recent call last):
testtools.testresult.real._StringException: Empty attachments:
  stderr
  stdout

pythonlogging:'': {{{
2015-07-31 09:37:44,661 17897 INFO     [tempest_lib.common.rest_client] Request (CreateRegisterImagesTest:test_register_http_image): 200 POST http://129.10.3.30:5000/v2.0/tokens
2015-07-31 09:37:44,661 17897 DEBUG    [tempest_lib.common.rest_client] Request - Headers: {}
        Body: None
    Response - Headers: {'status': '200', 'content-length': '10038', 'vary': 'X-Auth-Token', 'connection': 'close', 'date': 'Fri, 31 Jul 2015 17:42:29 GMT', 'content-type': 'application/json', 'x-openstack-request-id': 'req-c1005f28-3dd6-4550-a019-1cba37d8c977'}
        Body: None
2015-07-31 09:37:45,437 17897 INFO     [tempest_lib.common.rest_client] Request (CreateRegisterImagesTest:test_register_http_image): 400 POST http://129.10.3.30:9292/v1/images 0.775s
2015-07-31 09:37:45,438 17897 DEBUG    [tempest_lib.common.rest_client] Request - Headers: {'x-image-meta-container_format': 'bare', 'X-Auth-Token': '<omitted>', 'x-image-meta-is_public': 'False', 'x-glance-api-copy-from': 'http://download.cirros-cloud.net/0.3.1/cirros-0.3.1-x86_64-uec.tar.gz', 'x-image-meta-disk_format': 'raw', 'x-image-meta-name': 'New Http Image'}
        Body: None
    Response - Headers: {'status': '400', 'content-length': '129', 'connection': 'close', 'date': 'Fri, 31 Jul 2015 17:42:29 GMT', 'content-type': 'text/plain; charset=UTF-8', 'x-openstack-request-id': 'req-req-e308a866-ca1e-44ed-bf4e-9492af15c775'}
        Body: 400 Bad Request

External sources are not supported: 'http://download.cirros-cloud.net/0.3.1/cirros-0.3.1-x86_64-uec.tar.gz'
}}}

Traceback (most recent call last):
  File "/root/tempest/tempest/api/image/v1/test_images.py", line 74, in test_register_http_image
    copy_from=CONF.image.http_image)
  File "/root/tempest/tempest/api/image/base.py", line 74, in create_image
    disk_format, **kwargs)
  File "/root/tempest/tempest/services/image/v1/json/image_client.py", line 169, in create_image
    resp, body = self.post('v1/images', None, headers)
  File "/root/tempest/.venv/lib/python2.7/site-packages/tempest_lib/common/rest_client.py", line 256, in post
    return self.request('POST', url, extra_headers, headers, body)
  File "/root/tempest/.venv/lib/python2.7/site-packages/tempest_lib/common/rest_client.py", line 643, in request
    resp, resp_body)
  File "/root/tempest/.venv/lib/python2.7/site-packages/tempest_lib/common/rest_client.py", line 700, in _error_checker
    raise exceptions.BadRequest(resp_body)
tempest_lib.exceptions.BadRequest: Bad request
Details: 400 Bad Request

External sources are not supported: 'http://download.cirros-cloud.net/0.3.1/cirros-0.3.1-x86_64-uec.tar.gz'


======================================================================
FAIL: setUpClass (tempest.api.image.v1.test_images.ListImagesTest)
----------------------------------------------------------------------
Traceback (most recent call last):
testtools.testresult.real._StringException: Traceback (most recent call last):
  File "/root/tempest/tempest/test.py", line 272, in setUpClass
    six.reraise(etype, value, trace)
  File "/root/tempest/tempest/test.py", line 265, in setUpClass
    cls.resource_setup()
  File "/root/tempest/tempest/api/image/v1/test_images.py", line 113, in resource_setup
    img1 = cls._create_remote_image('one', 'bare', 'raw')
  File "/root/tempest/tempest/api/image/v1/test_images.py", line 147, in _create_remote_image
    location=location)
  File "/root/tempest/tempest/api/image/base.py", line 74, in create_image
    disk_format, **kwargs)
  File "/root/tempest/tempest/services/image/v1/json/image_client.py", line 169, in create_image
    resp, body = self.post('v1/images', None, headers)
  File "/root/tempest/.venv/lib/python2.7/site-packages/tempest_lib/common/rest_client.py", line 256, in post
    return self.request('POST', url, extra_headers, headers, body)
  File "/root/tempest/.venv/lib/python2.7/site-packages/tempest_lib/common/rest_client.py", line 643, in request
    resp, resp_body)
  File "/root/tempest/.venv/lib/python2.7/site-packages/tempest_lib/common/rest_client.py", line 700, in _error_checker
    raise exceptions.BadRequest(resp_body)
tempest_lib.exceptions.BadRequest: Bad request
Details: 400 Bad Request

External sources are not supported: 'http://download.cirros-cloud.net/0.3.1/cirros-0.3.1-x86_64-uec.tar.gz'


======================================================================
FAIL: tempest.api.image.v1.test_images.CreateRegisterImagesTest.test_register_remote_image[id-69da74d9-68a9-404b-9664-ff7164ccb0f5]
----------------------------------------------------------------------
Traceback (most recent call last):
testtools.testresult.real._StringException: Empty attachments:
  stderr
  stdout

pythonlogging:'': {{{
2015-07-31 09:37:51,284 17897 INFO     [tempest_lib.common.rest_client] Request (CreateRegisterImagesTest:test_register_remote_image): 400 POST http://129.10.3.30:9292/v1/images 0.629s
2015-07-31 09:37:51,285 17897 DEBUG    [tempest_lib.common.rest_client] Request - Headers: {'x-image-meta-container_format': 'bare', 'x-image-meta-location': 'http://download.cirros-cloud.net/0.3.1/cirros-0.3.1-x86_64-uec.tar.gz', 'X-Auth-Token': '<omitted>', 'x-image-meta-property-key1': 'value1', 'x-image-meta-property-key2': 'value2', 'x-image-meta-is_public': 'False', 'x-image-meta-disk_format': 'raw', 'x-image-meta-name': 'New Remote Image'}
        Body: None
    Response - Headers: {'status': '400', 'content-length': '129', 'connection': 'close', 'date': 'Fri, 31 Jul 2015 17:42:35 GMT', 'content-type': 'text/plain; charset=UTF-8', 'x-openstack-request-id': 'req-req-5dbde991-8da7-4498-8df7-0b48df28f7df'}
        Body: 400 Bad Request

External sources are not supported: 'http://download.cirros-cloud.net/0.3.1/cirros-0.3.1-x86_64-uec.tar.gz'
}}}

Traceback (most recent call last):
  File "/root/tempest/tempest/api/image/v1/test_images.py", line 60, in test_register_remote_image
    'key2': 'value2'})
  File "/root/tempest/tempest/api/image/base.py", line 74, in create_image
    disk_format, **kwargs)
  File "/root/tempest/tempest/services/image/v1/json/image_client.py", line 169, in create_image
    resp, body = self.post('v1/images', None, headers)
  File "/root/tempest/.venv/lib/python2.7/site-packages/tempest_lib/common/rest_client.py", line 256, in post
    return self.request('POST', url, extra_headers, headers, body)
  File "/root/tempest/.venv/lib/python2.7/site-packages/tempest_lib/common/rest_client.py", line 643, in request
    resp, resp_body)
  File "/root/tempest/.venv/lib/python2.7/site-packages/tempest_lib/common/rest_client.py", line 700, in _error_checker
    raise exceptions.BadRequest(resp_body)
tempest_lib.exceptions.BadRequest: Bad request
Details: 400 Bad Request

External sources are not supported: 'http://download.cirros-cloud.net/0.3.1/cirros-0.3.1-x86_64-uec.tar.gz'


Ran 48 tests in 84.291s

FAILED (failures=3)
00} \
OS_TEST_LOCK_PATH=${OS_TEST_LOCK_PATH:-${TMPDIR:-'/tmp'}} \
${PYTHON:-python} -m subunit.run discover -t ${OS_TOP_LEVEL:-./} ${OS_TEST_PATH:-./tempest/test_discover}  --load-list /tmp/tmp35Z4eh
running=OS_STDOUT_CAPTURE=${OS_STDOUT_CAPTURE:-1} \
OS_STDERR_CAPTURE=${OS_STDERR_CAPTURE:-1} \
OS_TEST_TIMEOUT=${OS_TEST_TIMEOUT:-500} \
OS_TEST_LOCK_PATH=${OS_TEST_LOCK_PATH:-${TMPDIR:-'/tmp'}} \
${PYTHON:-python} -m subunit.run discover -t ${OS_TOP_LEVEL:-./} ${OS_TEST_PATH:-./tempest/test_discover}  --load-list /tmp/tmpnPjpt3
running=OS_STDOUT_CAPTURE=${OS_STDOUT_CAPTURE:-1} \
OS_STDERR_CAPTURE=${OS_STDERR_CAPTURE:-1} \
OS_TEST_TIMEOUT=${OS_TEST_TIMEOUT:-500} \
OS_TEST_LOCK_PATH=${OS_TEST_LOCK_PATH:-${TMPDIR:-'/tmp'}} \
${PYTHON:-python} -m subunit.run discover -t ${OS_TOP_LEVEL:-./} ${OS_TEST_PATH:-./tempest/test_discover}  --load-list /tmp/tmp7VCbHI
