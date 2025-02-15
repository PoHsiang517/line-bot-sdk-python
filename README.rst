LINE Messaging API SDK for Python
=================================

|Build Status| |PyPI version| |Documentation Status|

SDK of the LINE Messaging API for Python.

Introduction
------------
The LINE Messaging API SDK for Python makes it easy to develop bots using LINE Messaging API, and you can create a sample bot within minutes.


Documentation
-------------

See the official API documentation for more information

English: https://developers.line.biz/en/docs/messaging-api/overview/

Japanese: https://developers.line.biz/ja/docs/messaging-api/overview/

Requirements
------------

-  Python >= 2.7 or >= 3.4

Installation
------------

::

    $ pip install line-bot-sdk

Synopsis
--------

Usage:

.. code:: python

    from flask import Flask, request, abort

    from linebot import (
        LineBotApi, WebhookHandler
    )
    from linebot.exceptions import (
        InvalidSignatureError
    )
    from linebot.models import (
        MessageEvent, TextMessage, TextSendMessage,
    )

    app = Flask(__name__)

    line_bot_api = LineBotApi('YOUR_CHANNEL_ACCESS_TOKEN')
    handler = WebhookHandler('YOUR_CHANNEL_SECRET')


    @app.route("/callback", methods=['POST'])
    def callback():
        # get X-Line-Signature header value
        signature = request.headers['X-Line-Signature']

        # get request body as text
        body = request.get_data(as_text=True)
        app.logger.info("Request body: " + body)

        # handle webhook body
        try:
            handler.handle(body, signature)
        except InvalidSignatureError:
            print("Invalid signature. Please check your channel access token/channel secret.")
            abort(400)

        return 'OK'


    @handler.add(MessageEvent, message=TextMessage)
    def handle_message(event):
        line_bot_api.reply_message(
            event.reply_token,
            TextSendMessage(text=event.message.text))


    if __name__ == "__main__":
        app.run()

API
---

LineBotApi
~~~~~~~~~~

\_\_init\_\_(self, channel\_access\_token, endpoint='https://api.line.me', timeout=5, http\_client=RequestsHttpClient)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Create a new LineBotApi instance.

.. code:: python

    line_bot_api = linebot.LineBotApi('YOUR_CHANNEL_ACCESS_TOKEN')

You can override the ``timeout`` value for each method.

reply\_message(self, reply\_token, messages, timeout=None)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Respond to events from users, groups, and rooms. You can get a
reply\_token from a webhook event object.

https://developers.line.me/en/docs/messaging-api/reference/#send-reply-message

.. code:: python

    line_bot_api.reply_message(reply_token, TextSendMessage(text='Hello World!'))

push\_message(self, to, messages, timeout=None)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Send messages to users, groups, and rooms at any time.

https://developers.line.me/en/docs/messaging-api/reference/#send-push-message

.. code:: python

    line_bot_api.push_message(to, TextSendMessage(text='Hello World!'))

multicast(self, to, messages, timeout=None)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Send messages to multiple users at any time.

https://developers.line.me/en/docs/messaging-api/reference/#send-multicast-messages

.. code:: python

    line_bot_api.multicast(['to1', 'to2'], TextSendMessage(text='Hello World!'))

get\_profile(self, user\_id, timeout=None)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Get user profile information.

https://developers.line.me/en/docs/messaging-api/reference/#get-profile

.. code:: python

    profile = line_bot_api.get_profile(user_id)

    print(profile.display_name)
    print(profile.user_id)
    print(profile.picture_url)
    print(profile.status_message)

get\_group\_member\_profile(self, group\_id, user\_id, timeout=None)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Gets the user profile of a member of a group that the bot is in. This can be
the user ID of a user who has not added the bot as a friend or has blocked
the bot.

https://developers.line.me/en/docs/messaging-api/reference/#get-group-member-profile

.. code:: python

    profile = line_bot_api.get_group_member_profile(group_id, user_id)

    print(profile.display_name)
    print(profile.user_id)
    print(profile.picture_url)

get\_room\_member\_profile(self, room\_id, user\_id, timeout=None)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Gets the user profile of a member of a room that the bot is in. This can be the
user ID of a user who has not added the bot as a friend or has blocked the bot.

https://developers.line.me/en/docs/messaging-api/reference/#get-room-member-profile

.. code:: python

    profile = line_bot_api.get_room_member_profile(room_id, user_id)

    print(profile.display_name)
    print(profile.user_id)
    print(profile.picture_url)

get\_group\_member\_ids(self, group\_id, start=None, timeout=None)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Gets the user IDs of the members of a group that the bot is in.
This includes the user IDs of users who have not added the bot as a friend or has blocked the bot.

https://developers.line.me/en/docs/messaging-api/reference/#get-group-member-user-ids

.. code:: python

    member_ids_res = line_bot_api.get_group_member_ids(group_id)

    print(member_ids_res.member_ids)
    print(member_ids_res.next)

get\_room\_member\_ids(self, room\_id, start=None, timeout=None)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Gets the user IDs of the members of a room that the bot is in.
This includes the user IDs of users who have not added the bot as a friend or has blocked the bot.

https://developers.line.me/en/docs/messaging-api/reference/#get-room-member-user-ids

.. code:: python

    member_ids_res = line_bot_api.get_room_member_ids(room_id)

    print(member_ids_res.member_ids)
    print(member_ids_res.next)

get\_message\_content(self, message\_id, timeout=None)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Retrieve image, video, and audio data sent by users.

https://developers.line.me/en/docs/messaging-api/reference/#get-content

.. code:: python

    message_content = line_bot_api.get_message_content(message_id)

    with open(file_path, 'wb') as fd:
        for chunk in message_content.iter_content():
            fd.write(chunk)

leave\_group(self, group\_id, timeout=None)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Leave a group.

https://developers.line.me/en/docs/messaging-api/reference/#leave-group

.. code:: python

    line_bot_api.leave_group(group_id)

leave\_room(self, room\_id, timeout=None)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Leave a room.

https://developers.line.me/en/docs/messaging-api/reference/#leave-room

.. code:: python

    line_bot_api.leave_room(room_id)

get\_rich\_menu(self, rich\_menu\_id, timeout=None)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Gets a rich menu via a rich menu ID.

https://developers.line.me/en/docs/messaging-api/reference/#get-rich-menu

.. code:: python

    rich_menu = line_bot_api.get_rich_menu(rich_menu_id)
    print(rich_menu.rich_menu_id)

create\_rich\_menu(self, rich\_menu, timeout=None)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Creates a rich menu.
You must upload a rich menu image and link the rich menu to a user for the rich menu to be displayed. You can create up to 10 rich menus for one bot.

https://developers.line.me/en/docs/messaging-api/reference/#create-rich-menu

.. code:: python

    rich_menu_to_create = RichMenu(
        size=RichMenuSize(width=2500, height=843),
        selected=False,
        name="Nice richmenu",
        chat_bar_text="Tap here",
        areas=[RichMenuArea(
            bounds=RichMenuBounds(x=0, y=0, width=2500, height=843),
            action=URIAction(label='Go to line.me', uri='https://line.me'))]
    )
    rich_menu_id = line_bot_api.create_rich_menu(rich_menu=rich_menu_to_create)
    print(rich_menu_id)

delete\_rich\_menu(self, rich\_menu\_id, timeout=None)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Deletes a rich menu.

https://developers.line.me/en/docs/messaging-api/reference/#delete-rich-menu
        
.. code:: python

    line_bot_api.delete_rich_menu(rich_menu_id)

get\_rich\_menu\_id\_of\_user(self, user\_id, timeout=None)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Gets the ID of the rich menu linked to a user.

https://developers.line.me/en/docs/messaging-api/reference/#get-rich-menu-id-of-user

.. code:: python

    rich_menu_id = ine_bot_api.get_rich_menu_id_of_user(user_id)
    print(rich_menu_id)

link\_rich\_menu\_to\_user(self, user\_id, rich\_menu\_id, timeout=None)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Links a rich menu to a user. Only one rich menu can be linked to a user at one time.

https://developers.line.me/en/docs/messaging-api/reference/#link-rich-menu-to-user

.. code:: python

    line_bot_api.link_rich_menu_to_user(user_id, rich_menu_id)

link\_rich\_menu\_to\_users(self, user\_ids, rich\_menu\_id, timeout=None)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Links a rich menu to multiple users.

https://developers.line.biz/en/reference/messaging-api/#link-rich-menu-to-users

.. code:: python

    line_bot_api.link_rich_menu_to_users(<user_ids>, <rich_menu_id>)

unlink\_rich\_menu\_from\_user(self, user\_id, timeout=None)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Unlinks a rich menu from a user.

https://developers.line.me/en/docs/messaging-api/reference/#unlink-rich-menu-from-user

.. code:: python

    line_bot_api.unlink_rich_menu_from_user(user_id)

unlink\_rich\_menu\_from\_users(self, user\_ids, timeout=None)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Unlinks rich menus from multiple users.

https://developers.line.biz/en/reference/messaging-api/#unlink-rich-menu-from-users

.. code:: python

    line_bot_api.unlink_rich_menu_from_users(<user_ids>)

get\_rich\_menu\_image(self, rich\_menu\_id, timeout=None)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Downloads an image associated with a rich menu.

https://developers.line.me/en/docs/messaging-api/reference/#download-rich-menu-image

.. code:: python

    content = line_bot_api.get_rich_menu_image(rich_menu_id)
    with open(file_path, 'wb') as fd:
        for chunk in content.iter_content():
            fd.write(chunk)

set\_rich\_menu\_image(self, rich\_menu\_id, content\_type, content, timeout=None)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Uploads and attaches an image to a rich menu.

https://developers.line.me/en/docs/messaging-api/reference/#upload-rich-menu-image

.. code:: python

    with open(file_path, 'rb') as f:
        line_bot_api.set_rich_menu_image(rich_menu_id, content_type, f)

get\_rich\_menu\_list(self, timeout=None)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Gets a list of all uploaded rich menus.

https://developers.line.me/en/docs/messaging-api/reference/#get-rich-menu-list

.. code:: python

    rich_menu_list = line_bot_api.get_rich_menu_list()
    for rich_menu in rich_menu_list:
        print(rich_menu.rich_menu_id)

set\_default\_rich\_menu(self, rich\_menu\_id, timeout=None)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Sets the default rich menu.

https://developers.line.biz/en/reference/messaging-api/#set-default-rich-menu

.. code:: python

    line_bot_api.set_default_rich_menu(<rich_menu_id>)

get\_default\_rich\_menu(self, timeout=None)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Gets the ID of the default rich menu set with the Messaging API.

https://developers.line.biz/en/reference/messaging-api/#get-default-rich-menu-id

.. code:: python

    line_bot_api.get_default_rich_menu()

cancel\_default\_rich\_menu(self, timeout=None)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Cancels the default rich menu set with the Messaging API.

https://developers.line.biz/en/reference/messaging-api/#cancel-default-rich-menu

.. code:: python

    line_bot_api.cancel_default_rich_menu()

issue\_link\_token(self, user\_id, timeout=None)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Issues a link token used for the account link feature.

https://developers.line.biz/en/reference/messaging-api/#issue-link-token

.. code:: python

    link_token_response = line_bot_api.issue_link_token(<user_id>)
    print(link_token_response)

issue\_channel\_token(self, client_id, client_secret, grant_type='client_credentials', timeout=None)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Issues a short-lived channel access token.

https://developers.line.biz/en/reference/messaging-api/#issue-channel-access-token

.. code:: python

    channel_token_response = line_bot_api.issue_channel_token(<client_id>, <client_secret>)
    print(access_token_response)

revoke\_channel\_token(self, access_token, timeout=None)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Revokes a channel access token.

https://developers.line.biz/en/reference/messaging-api/#revoke-channel-access-token

.. code:: python

    line_bot_api.revoke_channel_token(<access_token>)

get\_insight\_message\_delivery(self, date, timeout=None)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Get the number of messages sent on a specified day.

https://developers.line.biz/en/reference/messaging-api/#get-number-of-delivery-messages

.. code:: python

    insight = line_bot_api.get_insight_message_delivery('20191231')
    print(insight.api_broadcast)

get\_insight\_followers(self, date, timeout=None)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Get the number of users who have added the bot on or before a specified date.

https://developers.line.biz/en/reference/messaging-api/#get-number-of-followers

.. code:: python

    insight = line_bot_api.get_insight_followers('20191231')
    print(insight.followers)

get\_insight\_demographic(self, timeout=None)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Retrieve the demographic attributes for a bot's friends.

https://developers.line.biz/en/reference/messaging-api/#get-demographic

.. code:: python

    insight = line_bot_api.get_insight_demographic()
    print(insight.genders)

※ Error handling
^^^^^^^^^^^^^^^^

If the LINE API server returns an error, LineBotApi raises LineBotApiError.

https://developers.line.me/en/docs/messaging-api/reference/#error-responses

.. code:: python

    try:
        line_bot_api.push_message('to', TextSendMessage(text='Hello World!'))
    except linebot.exceptions.LineBotApiError as e:
        print(e.status_code)
        print(e.error.message)
        print(e.error.details)

Message objects
~~~~~~~~~~~~~~~

https://developers.line.me/en/docs/messaging-api/reference/#message-objects

The following classes are found in the ``linebot.models`` package.

TextSendMessage
^^^^^^^^^^^^^^^

.. code:: python

    text_message = TextSendMessage(text='Hello, world')

ImageSendMessage
^^^^^^^^^^^^^^^^

.. code:: python

    image_message = ImageSendMessage(
        original_content_url='https://example.com/original.jpg',
        preview_image_url='https://example.com/preview.jpg'
    )

VideoSendMessage
^^^^^^^^^^^^^^^^

.. code:: python

    video_message = VideoSendMessage(
        original_content_url='https://example.com/original.mp4',
        preview_image_url='https://example.com/preview.jpg'
    )

AudioSendMessage
^^^^^^^^^^^^^^^^

.. code:: python

    audio_message = AudioSendMessage(
        original_content_url='https://example.com/original.m4a',
        duration=240000
    )

LocationSendMessage
^^^^^^^^^^^^^^^^^^^

.. code:: python

    location_message = LocationSendMessage(
        title='my location',
        address='Tokyo',
        latitude=35.65910807942215,
        longitude=139.70372892916203
    )

StickerSendMessage
^^^^^^^^^^^^^^^^^^

.. code:: python

    sticker_message = StickerSendMessage(
        package_id='1',
        sticker_id='1'
    )

ImagemapSendMessage
^^^^^^^^^^^^^^^^^^^

.. code:: python

    imagemap_message = ImagemapSendMessage(
        base_url='https://example.com/base',
        alt_text='this is an imagemap',
        base_size=BaseSize(height=1040, width=1040),
        video=Video(
            original_content_url='https://example.com/video.mp4',
            preview_image_url='https://example.com/video_preview.jpg',
            area=ImagemapArea(
                x=0, y=0, width=1040, height=585
            ),
            external_link=ExternalLink(
                link_uri='https://example.com/see_more.html',
                label='See More',
            ),
        ),
        actions=[
            URIImagemapAction(
                link_uri='https://example.com/',
                area=ImagemapArea(
                    x=0, y=0, width=520, height=1040
                )
            ),
            MessageImagemapAction(
                text='hello',
                area=ImagemapArea(
                    x=520, y=0, width=520, height=1040
                )
            )
        ]
    )

TemplateSendMessage - ButtonsTemplate
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: python

    buttons_template_message = TemplateSendMessage(
        alt_text='Buttons template',
        template=ButtonsTemplate(
            thumbnail_image_url='https://example.com/image.jpg',
            title='Menu',
            text='Please select',
            actions=[
                PostbackAction(
                    label='postback',
                    display_text='postback text',
                    data='action=buy&itemid=1'
                ),
                MessageAction(
                    label='message',
                    text='message text'
                ),
                URIAction(
                    label='uri',
                    uri='http://example.com/'
                )
            ]
        )
    )

TemplateSendMessage - ConfirmTemplate
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: python

    confirm_template_message = TemplateSendMessage(
        alt_text='Confirm template',
        template=ConfirmTemplate(
            text='Are you sure?',
            actions=[
                PostbackAction(
                    label='postback',
                    display_text='postback text',
                    data='action=buy&itemid=1'
                ),
                MessageAction(
                    label='message',
                    text='message text'
                )
            ]
        )
    )

TemplateSendMessage - CarouselTemplate
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: python

    carousel_template_message = TemplateSendMessage(
        alt_text='Carousel template',
        template=CarouselTemplate(
            columns=[
                CarouselColumn(
                    thumbnail_image_url='https://example.com/item1.jpg',
                    title='this is menu1',
                    text='description1',
                    actions=[
                        PostbackAction(
                            label='postback1',
                            display_text='postback text1',
                            data='action=buy&itemid=1'
                        ),
                        MessageAction(
                            label='message1',
                            text='message text1'
                        ),
                        URIAction(
                            label='uri1',
                            uri='http://example.com/1'
                        )
                    ]
                ),
                CarouselColumn(
                    thumbnail_image_url='https://example.com/item2.jpg',
                    title='this is menu2',
                    text='description2',
                    actions=[
                        PostbackAction(
                            label='postback2',
                            display_text='postback text2',
                            data='action=buy&itemid=2'
                        ),
                        MessageAction(
                            label='message2',
                            text='message text2'
                        ),
                        URIAction(
                            label='uri2',
                            uri='http://example.com/2'
                        )
                    ]
                )
            ]
        )
    )

TemplateSendMessage - ImageCarouselTemplate
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: python

    image_carousel_template_message = TemplateSendMessage(
        alt_text='ImageCarousel template',
        template=ImageCarouselTemplate(
            columns=[
                ImageCarouselColumn(
                    image_url='https://example.com/item1.jpg',
                    action=PostbackAction(
                        label='postback1',
                        display_text='postback text1',
                        data='action=buy&itemid=1'
                    )
                ),
                ImageCarouselColumn(
                    image_url='https://example.com/item2.jpg',
                    action=PostbackAction(
                        label='postback2',
                        display_text='postback text2',
                        data='action=buy&itemid=2'
                    )
                )
            ]
        )
    )

With QuickReply
^^^^^^^^^^^^^^^

.. code:: python

    text_message = TextSendMessage(text='Hello, world',
                                   quick_reply=QuickReply(items=[
                                       QuickReplyButton(action=MessageAction(label="label", text="text"))
                                   ]))

Webhook
-------

WebhookParser
~~~~~~~~~~~~~

※ You can use WebhookParser or WebhookHandler

\_\_init\_\_(self, channel\_secret)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: python

    parser = linebot.WebhookParser('YOUR_CHANNEL_SECRET')

parse(self, body, signature, as_payload=False)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Parses the webhook body, and returns a list of Event objects or a WebhookPayload object (depending on as_payload).
If the signature does NOT match, ``InvalidSignatureError`` is raised.

.. code:: python

    events = parser.parse(body, signature)

    for event in events:
        do_something(event)

.. code:: python

    payload = parser.parse(body, signature, as_payload=True)

    for event in payload.events:
        do_something(payload.event, payload.destination)

WebhookHandler
~~~~~~~~~~~~~~

※ You can use WebhookParser or WebhookHandler

\_\_init\_\_(self, channel\_secret)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: python

    handler = linebot.WebhookHandler('YOUR_CHANNEL_SECRET')

handle(self, body, signature)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Handles webhooks with **handlers** added
by the decorators `add <#add-self-event-message-none>`__ and `default <#default-self>`__.
If the signature does NOT match, ``InvalidSignatureError`` is raised.

.. code:: python

    handler.handle(body, signature)

add(self, event, message=None)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Add a **handler** method by using this decorator.

.. code:: python

    @handler.add(MessageEvent, message=TextMessage)
    def handle_message(event):
        line_bot_api.reply_message(
            event.reply_token,
            TextSendMessage(text=event.message.text))

When the event is an instance of MessageEvent and event.message is an instance of TextMessage,
this handler method is called.

.. code:: python

    @handler.add(MessageEvent)
    def handle_message(event, destination):
        # do something

If the arity of the handler method is more than one,
a destination property in a webhook request is passed to it as the second argument.

.. code:: python

    @handler.add(FollowEvent)
    def handle_follow():
        # do something

If the arity of the handler method is zero, the handler method is called with no arguments.

default(self)
^^^^^^^^^^^^^

Set the default **handler** method by using this decorator.

.. code:: python

    @handler.default()
    def default(event):
        print(event)

If there is no handler for an event, this default handler method is called.

WebhookPayload
~~~~~~~~~~~~~~~

https://developers.line.biz/en/reference/messaging-api/#request-body

- WebhookPayload
    - destination
    - events: list[`Event <#event>`__]

Webhook event object
~~~~~~~~~~~~~~~~~~~~

https://developers.line.me/en/docs/messaging-api/reference/#webhook-event-objects

The following classes are found in the ``linebot.models`` package.

`Event <https://line-bot-sdk-python.readthedocs.io/en/stable/linebot.models.html#module-linebot.models.events>`__
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- MessageEvent
    - type
    - timestamp
    - source: `Source <#source>`__
    - reply\_token
    - message: `Message <#message>`__
- FollowEvent
    - type
    - timestamp
    - source: `Source <#source>`__
    - reply\_token
- UnfollowEvent
    - type
    - timestamp
    - source: `Source <#source>`__
- JoinEvent
    - type
    - timestamp
    - source: `Source <#source>`__
    - reply\_token
- LeaveEvent
    - type
    - timestamp
    - source: `Source <#source>`__
- PostbackEvent
    - type
    - timestamp
    - source: `Source <#source>`__
    - reply\_token
    - postback: Postback
        - data
        - params: dict
- BeaconEvent
    - type
    - timestamp
    - source: `Source <#source>`__
    - reply\_token
    - beacon: Beacon
        - type
        - hwid
        - device_message
- MemberJoinedEvent
    - type
    - timestamp
    - source: `Source <#source>`__
    - reply\_token
    - joined: Joined
- MemberLeftEvent
    - type
    - timestamp
    - source: `Source <#source>`__
    - reply\_token
    - left: Left
- AccountLinkEvent
    - type
    - timestamp
    - source: `Source <#source>`__
    - reply\_token
    - link: Link
- ThingsEvent
    - type
    - timestamp
    - source: `Source <#source>`__
    - reply\_token
    - things: DeviceLink | DeviceUnlink | ScenarioResult

Source
^^^^^^

- SourceUser
    - type
    - user\_id
- SourceGroup
    - type
    - group\_id
    - user\_id
- SourceRoom
    - type
    - room\_id
    - user\_id

Message
^^^^^^^

- TextMessage
    - type
    - id
    - text
- ImageMessage
    - type
    - id
    - content_provider
- VideoMessage
    - type
    - id
    - duration
    - content_provider
- AudioMessage
    - type
    - id
    - duration
    - content_provider
- LocationMessage
    - type
    - id
    - title
    - address
    - latitude
    - longitude
- StickerMessage
    - type
    - id
    - package\_id
    - sticker\_id
- FileMessage
    - type
    - id
    - file\_size
    - file\_name

Hints
-----

Examples
~~~~~~~~

`simple-server-echo <https://github.com/line/line-bot-sdk-python/tree/master/examples/simple-server-echo>`__
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Sample echo-bot using
`wsgiref.simple\_server <https://docs.python.org/3/library/wsgiref.html>`__

`flask-echo <https://github.com/line/line-bot-sdk-python/tree/master/examples/flask-echo>`__
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Sample echo-bot using `Flask <http://flask.pocoo.org/>`__

`flask-kitchensink <https://github.com/line/line-bot-sdk-python/tree/master/examples/flask-kitchensink>`__
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Sample bot using `Flask <http://flask.pocoo.org/>`__

API documentation
-----------------

::

    $ cd docs
    $ make html
    $ open build/html/index.html

OR |Documentation Status|

Help and media
--------------
FAQ: https://developers.line.biz/en/faq/

Community Q&A: https://www.line-community.me/questions

News: https://developers.line.biz/en/news/

Twitter: @LINE_DEV 

Versioning
----------
This project respects semantic versioning

See http://semver.org/

Contributing
------------
Please check `CONTRIBUTING <CONTRIBUTING.md>`__ before making a contribution.

For SDK developers
------------------

First install for development.

::

    $ pip install -r requirements-dev.txt

Run tests
~~~~~~~~~

Test by using tox. We test against the following versions.

-  2.7
-  3.4
-  3.5
-  3.6
-  3.7

To run all tests and to run ``flake8`` against all versions, use:

::

    tox

To run all tests against version 2.7, use:

::

    $ tox -e py27

To run a test against version 2.7 and against a specific file, use:

::

    $ tox -e py27 -- tests/test_webhook.py

And more... TBD

.. |Build Status| image:: https://travis-ci.org/line/line-bot-sdk-python.svg?branch=master
   :target: https://travis-ci.org/line/line-bot-sdk-python
.. |PyPI version| image:: https://badge.fury.io/py/line-bot-sdk.svg
   :target: https://badge.fury.io/py/line-bot-sdk
.. |Documentation Status| image:: https://readthedocs.org/projects/line-bot-sdk-python/badge/?version=stable
   :target: http://line-bot-sdk-python.readthedocs.io/en/stable

License
--------

::

    Copyright (C) 2016 LINE Corp.

    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.
