# ros2-bluetooth-node-
import bluetooth
import rclpy
from rclpy.node import Node
from std_msgs.msg import String
import bluetooth
class MinimalSubscriber(Node):

    def __init__(self):
        super().__init__('minimal_subscriber')
        self.subscription = self.create_subscription(
            String,
            'topic',
            self.listener_callback,
            10)
        self.subscription  # prevent unused variable warning

    def listener_callback(self, msg):
        self.get_logger().info('I heard: "%s"' % msg.data)
        addr = msg.data
        uuid = "94f39d29-7d6d-437d-973b-fba39e49d4ee"
        service_matches = bluetooth.find_service( uuid = uuid, address = addr )

        if len(service_matches) == 0:
            print ("Couldn't find the SampleServer service.")
            return

        first_match = service_matches[0]
        port = first_match["port"]
        name = first_match["name"]
        host = first_match["host"]

        print ("Connecting to \"%s\" on %s" % (name, host))

        # Create the client socket
        sock=bluetooth.BluetoothSocket( bluetooth.RFCOMM )
        sock.connect((host, port))

        print ("Connected. Type something...")
        while True:
            data = input()
            if len(data) == 0:
                break
            sock.send(data)

        sock.close()
        
