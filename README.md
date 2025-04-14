# hallel-app
import React, { useState, useEffect, useRef, useCallback } from 'react';
import { motion, AnimatePresence } from 'framer-motion';
import {
  User,
  Users,
  MessageCircle,
  Video,
  Phone,
  Search,
  Plus,
  MoreHorizontal,
  X,
  Send,
  Camera,
  Mic,
  MicOff,
  VideoOff,
  Maximize,
  Minimize,
  AlertTriangle,
  Loader2,
  Settings,
  LogOut,
  Info,
  CheckCircle,
  Bell,
  Home,
  FileText,
  Edit,
  Trash2,
  List,
  Grid,
} from 'lucide-react';
import { Button } from '@/components/ui/button';
import { Input } from '@/components/ui/input';
import { Textarea } from '@/components/ui/textarea';
import { Avatar, AvatarImage, AvatarFallback } from '@/components/ui/avatar';
import {
  Sheet,
  SheetContent,
  SheetHeader,
  SheetTitle,
  SheetDescription,
  SheetTrigger,
  SheetClose
} from '@/components/ui/sheet';
import {
    DropdownMenu,
    DropdownMenuContent,
    DropdownMenuItem,
    DropdownMenuLabel,
    DropdownMenuSeparator,
    DropdownMenuTrigger,
} from "@/components/ui/dropdown-menu"
import { cn } from '@/lib/utils';

// ===============================
// Mock Data (Replace with actual data fetching and database integration)
// ===============================

const mockUsers = [
  { id: '1', name: 'Alice Smith', avatar: 'https://source.unsplash.com/random/100x100/?portrait&1', email: 'alice@example.com', bio: 'Software Engineer' },
  { id: '2', name: 'Bob Johnson', avatar: 'https://source.unsplash.com/random/100x100/?portrait&2', email: 'bob@example.com', bio: 'Web Developer' },
  { id: '3', name: 'Charlie Brown', avatar: 'https://source.unsplash.com/random/100x100/?portrait&3', email: 'charlie@example.com', bio: 'Designer' },
  { id: '4', name: 'Diana Miller', avatar: 'https://source.unsplash.com/random/100x100/?portrait&4', email: 'diana@example.com', bio: 'Project Manager' },
  { id: '5', name: 'Ethan Davis', avatar: 'https://source.unsplash.com/random/100x100/?portrait&5', email: 'ethan@example.com', bio: 'Data Scientist' },
  { id: '6', name: 'Fiona Wilson', avatar: 'https://source.unsplash.com/random/100x100/?portrait&6', email: 'fiona@example.com', bio: 'Marketing Specialist' },
  { id: '7', name: 'George Lee', avatar: 'https://source.unsplash.com/random/100x100/?portrait&7', email: 'george@example.com', bio: 'Sales Representative' },
  { id: '8', name: 'Hannah Clark', avatar: 'https://source.unsplash.com/random/100x100/?portrait&8', email: 'hannah@example.com', bio: 'UX/UI Designer' },
];

const mockMessages = [
  { id: 'm1', userId: '1', text: 'Hello!', timestamp: Date.now() - 60000, reactions: [] },
  { id: 'm2', userId: '2', text: 'Hi Alice, how are you?', timestamp: Date.now() - 58000, reactions: [{ userId: '1', emoji: '👍' }] },
  { id: 'm3', userId: '1', text: 'I\'m good, thanks! What about you?', timestamp: Date.now() - 55000, reactions: [] },
  { id: 'm4', userId: '3', text: 'Hey guys!', timestamp: Date.now() - 50000, reactions: [] },
  { id: 'm5', userId: '2', text: 'Doing well, Charlie!', timestamp: Date.now() - 48000, reactions: [] },
  { id: 'm6', userId: '1', text: 'This is a long message to test how the text wraps and handles newlines. It should wrap to the next line and not overflow. Testing testing 123.', timestamp: Date.now() - 40000, reactions: [] },
  { id: 'm7', userId: '1', text: '👍', timestamp: Date.now() - 39000, reactions: [] },
];

const mockRooms = [
  { id: 'room1', name: 'General Chat', participants: ['1', '2', '3', '4'], type: 'public' },
  { id: 'room2', name: 'Project Alpha', participants: ['1', '3', '5'], type: 'private' },
  { id: 'room3', name: 'Team Social', participants: ['2', '4', '6', '7', '8'], type: 'public' },
];

const mockPosts = [
    { id: 'post1', userId: '1', text: 'Just had a great day!', timestamp: Date.now() - 86400000, likes: ['2', '3'], comments: [{ userId: '2', text: 'Awesome!' }] },
    { id: 'post2', userId: '2', text: 'Anyone knows a good React tutorial?', timestamp: Date.now() - 172800000, likes: ['1', '4'], comments: [] },
];

const mockNotifications = [
  { id: 'n1', userId: '2', type: 'like', sourceUserId: '1', sourceUserName: 'Alice Smith', timestamp: Date.now() - 3600000, read: false, postId: 'post1' },
  { id: 'n2', userId: '3', type: 'comment', sourceUserId: '1', sourceUserName: 'Alice Smith', timestamp: Date.now() - 1800000, read: true, postId: 'post1' },
];

// ===============================
// Utility Functions
// ===============================

const getUserById = (id: string) => mockUsers.find(user => user.id === id);

// ===============================
// Components
// ===============================

// Message Component
const Message = ({ message, currentUserId }: { message: typeof mockMessages[0], currentUserId: string }) => {
  const user = getUserById(message.userId);
  const isCurrentUser = message.userId === currentUserId;

  const getReactionCount = (emoji: string) => {
    return message.reactions.filter(r => r.emoji === emoji).length;
  };

  return (
    <div
      className={cn(
        'flex w-full mb-2',
        isCurrentUser ? 'justify-end' : 'justify-start'
      )}
    >
      <div
        className={cn(
          'rounded-xl px-3 py-2 max-w-[70%] relative',
          isCurrentUser
            ? 'bg-blue-500 text-white ml-auto'
            : 'bg-gray-200 text-gray-800 mr-auto'
        )}
      >
        <div className="flex items-center gap-2 mb-1">
          {!isCurrentUser && user && (
            <>
              <Avatar className="h-6 w-6">
                <AvatarImage src={user.avatar} alt={user.name} />
                <AvatarFallback>{user.name.substring(0, 2)}</AvatarFallback>
              </Avatar>
              <span className="text-xs font-medium">{user.name}</span>
            </>
          )}
          {isCurrentUser && (
            <span className="text-xs font-medium absolute top-[-1.4rem] right-2 text-blue-300">You</span>
          )}
        </div>
        <p className="text-sm whitespace-pre-wrap break-words">{message.text}</p>
        <span className="text-xs opacity-70 absolute bottom-1 right-2">
          {new Date(message.timestamp).toLocaleTimeString([], { hour: 'numeric', minute: '2-digit' })}
        </span>
        <div className="absolute flex gap-1 bottom-1 left-1">
          {getReactionCount('👍') > 0 && (
            <span className="text-xs p-0.5 rounded-full bg-gray-300 text-gray-800 flex items-center">
              👍{getReactionCount('👍')}
            </span>
          )}
        </div>
      </div>
    </div>
  );
};

// Chat Room Component
const ChatRoom = ({
  roomId,
  currentUserId,
  onVideoCallStart,
  onVoiceCallStart,
  onMessageSend,
  onRoomLeave
}: {
  roomId: string,
  currentUserId: string,
  onVideoCallStart: (roomId: string) => void,
  onVoiceCallStart: (roomId: string) => void,
  onMessageSend: (roomId: string, message: string) => void,
  onRoomLeave: (roomId: string) => void
}) => {
  const [messages, setMessages] = useState(mockMessages.filter(m => m.id === 'm1' || m.id === 'm2' || m.id === 'm3')); // Replace with actual message fetching
  const [newMessage, setNewMessage] = useState('');
  const messagesEndRef = useRef<HTMLDivElement>(null);
  const room = mockRooms.find(r => r.id === roomId);

  // Scroll to bottom on new message
  useEffect(() => {
    messagesEndRef.current?.scrollIntoView({ behavior: 'smooth' });
  }, [messages]);

  const handleSendMessage = () => {
    if (newMessage.trim()) {
      onMessageSend(roomId, newMessage);
      setMessages(prevMessages => [...prevMessages, {
        id: `m${Date.now()}`,
        userId: currentUserId,
        text: newMessage,
        timestamp: Date.now(),
        reactions: []
      }]);
      setNewMessage('');
    }
  };

  if (!room) {
    return <div className="flex-1 flex items-center justify-center">Room not found.</div>;
  }

  return (
    <div className="flex flex-col h-full bg-gray-100">
      {/* Room Header */}
      <div className="bg-white p-4 border-b border-gray-200 flex items-center justify-between">
        <h2 className="text-lg font-semibold">{room.name}</h2>
        <div className="flex gap-2">
          <Button variant="outline" onClick={() => onVoiceCallStart(roomId)}>
            <Phone className="mr-2 h-4 w-4" /> Voice Call
          </Button>
          <Button variant="outline" onClick={() => onVideoCallStart(roomId)}>
            <Video className="mr-2 h-4 w-4" /> Video Call
          </Button>
          <DropdownMenu>
            <DropdownMenuTrigger asChild>
              <Button variant="ghost">
                <MoreHorizontal className="h-4 w-4" />
              </Button>
            </DropdownMenuTrigger>
            <DropdownMenuContent align="end">
              <DropdownMenuLabel>Room Options</DropdownMenuLabel>
              <DropdownMenuSeparator />
              <DropdownMenuItem onSelect={() => onRoomLeave(roomId)}>
                <LogOut className="mr-2 h-4 w-4" /> Leave Room
              </DropdownMenuItem>
              {/* Add more options like muting, notifications, etc. */}
            </DropdownMenuContent>
          </DropdownMenu>
        </div>
      </div>

      {/* Message List */}
      <div className="flex-1 overflow-y-auto p-4">
        {messages.map(message => (
          <Message key={message.id} message={message} currentUserId={currentUserId} />
        ))}
        <div ref={messagesEndRef} />
      </div>

      {/* Message Input */}
      <div className="bg-white p-4 border-t border-gray-200 flex items-center gap-2">
        <Textarea
          placeholder="Type your message..."
          value={newMessage}
          onChange={(e) => setNewMessage(e.target.value)}
          onKeyDown={(e) => {
            if (e.key === 'Enter' && !e.shiftKey) {
              e.preventDefault();
              handleSendMessage();
            }
          }}
          className="flex-1 resize-none"
          rows={1}
        />
        <Button onClick={handleSendMessage} disabled={!newMessage.trim()}>
          <Send className="h-4 w-4" />
        </Button>
      </div>
    </div>
  );
};

// User List Component
const UserList = ({ users, onStartDirectChat }: { users: typeof mockUsers, onStartDirectChat: (userId: string) => void }) => {
  const [searchTerm, setSearchTerm] = useState('');
  const filteredUsers = users.filter(user =>
    user.name.toLowerCase().includes(searchTerm.toLowerCase())
  );

  return (
    <div className="w-64 bg-gray-100 border-r border-gray-200 p-4">
      <Input
        type="text"
        placeholder="Search users..."
        value={searchTerm}
        onChange={(e) => setSearchTerm(e.target.value)}
        className="mb-4"
      />
      <h2 className="text-lg font-semibold mb-2">Online Users</h2>
      <div className="space-y-2">
        {filteredUsers.map(user => (
          <div
            key={user.id}
            className="flex items-center gap-2 p-2 rounded-md hover:bg-gray-200 cursor-pointer"
            onClick={() => onStartDirectChat(user.id)}
          >
            <Avatar className="h-8 w-8">
              <AvatarImage src={user.avatar} alt={user.name} />
              <AvatarFallback>{user.name.substring(0, 2)}</AvatarFallback>
            </Avatar>
            <span className="text-sm font-medium">{user.name}</span>
          </div>
        ))}
      </div>
    </div>
  );
};

// Sidebar Component
const Sidebar = ({
  rooms,
  onCreateRoom,
  onJoinRoom,
  onShowProfile,
  onShowSettings,
  onLogout,
  onShowFeed
}: {
  rooms: typeof mockRooms,
  onCreateRoom: (roomName: string) => void,
  onJoinRoom: (roomId: string) => void,
  onShowProfile: () => void,
  onShowSettings: () => void,
  onLogout: () => void,
  onShowFeed: () => void
}) => {
  const [isCreatingRoom, setIsCreatingRoom] = useState(false);
  const [newRoomName, setNewRoomName] = useState('');
  const [searchTerm, setSearchTerm] = useState('');

  const filteredRooms = rooms.filter((room) =>
    room.name.toLowerCase().includes(searchTerm.toLowerCase())
  );

  const handleCreateRoom = () => {
    if (newRoomName.trim()) {
      onCreateRoom(newRoomName);
      setIsCreatingRoom(false);
      setNewRoomName('');
    }
  };

  return (
    <div className="w-64 bg-white border-r border-gray-200 p-4">
      <div className="flex items-center justify-between mb-4">
        <h1 className="text-xl font-bold">SwiftChat</h1>
        <DropdownMenu>
          <DropdownMenuTrigger asChild>
            <Button variant="ghost">
              <Settings className="h-5 w-5" />
            </Button>
          </DropdownMenuTrigger>
          <DropdownMenuContent align="end">
            <DropdownMenuLabel>My Account</DropdownMenuLabel>
            <DropdownMenuSeparator />
            <DropdownMenuItem onSelect={onShowProfile}>
              <User className="mr-2 h-4 w-4" /> Profile
            </DropdownMenuItem>
            <DropdownMenuItem onSelect={onShowSettings}>
              <Settings className="mr-2 h-4 w-4" /> Settings
            </DropdownMenuItem>
            <DropdownMenuSeparator />
            <DropdownMenuItem onSelect={onLogout}>
              <LogOut className="mr-2 h-4 w-4" /> Log out
            </DropdownMenuItem>
          </DropdownMenuContent>
        </DropdownMenu>
      </div>

      <Button
        variant="outline"
        className="mb-4 w-full"
        onClick={onShowFeed} // New button to show the feed
      >
        <Home className="mr-2 h-4 w-4" />
        Feed
      </Button>

      <Input
        type="text"
        placeholder="Search rooms..."
        value={searchTerm}
        onChange={(e) => setSearchTerm(e.target.value)}
        className="mb-4"
      />

      <h2 className="text-lg font-semibold mb-2">Rooms</h2>
      <div className="space-y-2">
        {filteredRooms.map(room => (
          <div
            key={room.id}
            className="p-2 rounded-md hover:bg-gray-100 cursor-pointer"
            onClick={() => onJoinRoom(room.id)}
          >
            {room.name}
          </div>
        ))}
      </div>

      <Button
        variant="outline"
        className="mt-4 w-full"
        onClick={() => setIsCreatingRoom(true)}
        disabled={isCreatingRoom}
      >
        <Plus className="mr-2 h-4 w-4" /> Create Room
      </Button>

      <AnimatePresence>
        {isCreatingRoom && (
          <motion.div
            initial={{ opacity: 0, height: 0 }}
            animate={{ opacity: 1, height: 'auto' }}
            exit={{ opacity: 0, height: 0 }}
            transition={{ duration: 0.2 }}
            className="mt-4 space-y-2"
          >
            <Input
              type="text"
              placeholder="Room name..."
              value={newRoomName}
              onChange={(e) => setNewRoomName(e.target.value)}
            />
            <div className="flex gap-2">
              <Button onClick={handleCreateRoom} disabled={!newRoomName.trim()}>
                Create
              </Button>
              <Button
                variant="outline"
                onClick={() => {
                  setIsCreatingRoom(false);
                  setNewRoomName('');
                }}
              >
                Cancel
              </Button>
            </div>
          </motion.div>
        )}
      </AnimatePresence>
    </div>
  );
};

// Video Call/Conference Component
const VideoCall = ({
  roomId,
  onClose,
  isInitiator,
  onToggleCamera,
  onToggleMic,
  isCameraOn,
  isMicOn
}: {
  roomId: string,
  onClose: () => void,
  isInitiator: boolean,
  onToggleCamera: () => void,
  onToggleMic: () => void,
  isCameraOn: boolean,
  isMicOn: boolean
}) => {
  const [remoteStreams, setRemoteStreams] = useState<any[]>([]); // Replace 'any' with the actual type
  const localVideoRef = useRef<HTMLVideoElement>(null);
  const mediaStreamRef = useRef<MediaStream | null>(null);
  const [isVideoCall, setIsVideoCall] = useState(true);

  // Get User Media (Camera and Microphone)
  const getMediaStream = useCallback(async () => {
    try {
      const stream = await navigator.mediaDevices.getUserMedia({
        video: true,
        audio: true,
      });
      mediaStreamRef.current = stream;
      if (localVideoRef.current) {
        localVideoRef.current.srcObject = stream;
      }
      return stream;
    } catch (error) {
      console.error('Error accessing media devices:', error);
      // Handle error (e.g., show a message to the user)
      return null;
    }
  }, []);

  // Set up WebRTC connection (using a library like 'peerjs' or a custom implementation)
  useEffect(() => {
    let peerConnection: RTCPeerConnection;
    let localStream: MediaStream | null;

    const initializeCall = async () => {
      localStream = await getMediaStream();
      if (!localStream) {
        return; // Exit if media stream is not available
      }

      // 1. Create a new RTCPeerConnection.
      peerConnection = new RTCPeerConnection({
        iceServers: [
          { urls: 'stun:stun.l.google.com:19302' }, // Google's STUN server
        ],
      });

      // 2. Add tracks from the local stream to the RTCPeerConnection.
      localStream.getTracks().forEach((track) => {
        peerConnection.addTrack(track, localStream!);
      });


      // 3. Handle ICE candidates.
      peerConnection.onicecandidate = (event) => {
        if (event.candidate) {
          // Send the ICE candidate to the other peer through your signaling server.
          // In a real app, you'd use Socket.IO, WebSockets, or another signaling method.
          console.log('Sending ICE candidate:', event.candidate);
          // For this example, we'll simulate sending it back to ourselves (for demonstration only).
          // In a real app, this is where you'd use your signaling server.
          // sendIceCandidate(event.candidate, roomId);  // Replace with actual signaling
        }
      };

      // 4. Handle remote tracks.
      peerConnection.ontrack = (event) => {
        console.log('Received remote track:', event.track);
        setRemoteStreams((prevStreams) => [...prevStreams, event.streams[0]]);
      };

      if (isInitiator) {
        // 5a. Initiator: Create an offer.
        const offer = await peerConnection.createOffer();

        // 6a. Initiator: Set the local description.
        await peerConnection.setLocalDescription(offer);

        // 7a. Initiator: Send the offer to the other peer.
        // sendOffer(offer, roomId); // Replace with actual signaling
        console.log("Offer Sent", offer)
        //Simulate Answer
        setTimeout(async () => {
          const answer = await peerConnection.createAnswer();
          await peerConnection.setRemoteDescription(answer)
        }, 3000)
      } else {
        // 5b. Receiver: Wait for an offer.
        // When you receive an offer from the initiator, you would do this:
        // peerConnection.setRemoteDescription(new RTCSessionDescription(offer));
        // const answer = await peerConnection.createAnswer();
        // await peerConnection.setLocalDescription(answer);
        // sendAnswer(answer, roomId);
        console.log("Waiting for offer")
      }
    }

    initializeCall();


    // Clean up
    return () => {
      if (peerConnection) {
        peerConnection.close();
      }
      if (mediaStreamRef.current) {
        mediaStreamRef.current.getTracks().forEach(track => track.stop());
        mediaStreamRef.current = null;
      }
    };
  }, [isInitiator, getMediaStream, roomId]);

  useEffect(() => {
    if (localVideoRef.current && mediaStreamRef.current) {
      localVideoRef.current.srcObject = mediaStreamRef.current;
    }
  }, []);

  return (
    <div className="fixed inset-0 bg-black bg-opacity-80 flex items-center justify-center z-50">
      <div className="bg-gray-800 rounded-xl p-4 w-full max-w-4xl h-full max-h-[80vh] overflow-y-auto relative">
        <h2 className="text-xl font-semibold text-white mb-4">Video Call ({roomId})</h2>
        <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
          {/* Local Video */}
          <div className="relative">
            <video
              ref={localVideoRef}
              autoPlay
              muted
              playsInline
              className="w-full rounded-md"
            />
            <div className="absolute bottom-2 left-2 bg-black/50 text-white px-2 py-1 rounded-md text-sm">
              You (Me)
            </div>
          </div>

          {/* Remote Videos */}
          {remoteStreams.map((stream, index) => (
            <div key={index} className="relative">
              <video
                autoPlay
                playsInline
                className="w-full rounded-md"
                srcObject={stream}
              />
              <div className="absolute bottom-2 left-2 bg-black/50 text-white px-2 py-1 rounded-md text-sm">
                Participant {index + 1}
              </div>
            </div>
          ))}
        </div>

        {/* Controls */}
        <div className="absolute bottom-4 left-1/2 transform -translate-x-1/2 flex gap-4">
          <Button
            variant="destructive"onClick={onClose}
            className="bg-red-500 hover:bg-red-600 text-white"
          >
            <X className="h-5 w-5" />
            Leave
          </Button>
          <Button
            variant="secondary"
            onClick={onToggleCamera}
            className={isCameraOn ? "bg-gray-700 hover:bg-gray-600 text-white" : "bg-gray-700 hover:bg-gray-600 text-red-500"}
          >
            {isCameraOn ? <Video className="h-5 w-5" /> : <VideoOff className="h-5 w-5" />}
            {isCameraOn ? "" : " Camera Off"}
          </Button>
          <Button
            variant="secondary"
            onClick={onToggleMic}
            className={isMicOn ? "bg-gray-700 hover:bg-gray-600 text-white" : "bg-gray-700 hover:bg-gray-600 text-red-500"}
          >
            {isMicOn ? <Mic className="h-5 w-5" /> : <MicOff className="h-5 w-5" />}
            {isMicOn ? "" : " Mic Off"}
          </Button>
          <Button
            variant="secondary"
            onClick={() => setIsVideoCall(!isVideoCall)}
            className="bg-gray-700 hover:bg-gray-600 text-white"
          >
            {isVideoCall ? <Video className="h-5 w-5" /> : <Phone className="h-5 w-5" />}
            {isVideoCall ? " Video" : " Voice"}
          </Button>
        </div>
      </div>
    </div>
  );
};

// Post Component
const Post = ({ post, currentUserId }: { post: typeof mockPosts[0], currentUserId: string }) => {
    const user = getUserById(post.userId);
    const [likes, setLikes] = useState(post.likes);
    const [comments, setComments] = useState(post.comments);
    const [newComment, setNewComment] = useState('');
    const [showComments, setShowComments] = useState(false);

    const handleLike = () => {
        if (likes.includes(currentUserId)) {
            setLikes(likes.filter(id => id !== currentUserId));
        } else {
            setLikes([...likes, currentUserId]);
        }
    };

    const handleAddComment = () => {
        if (newComment.trim()) {
            const comment = { userId: currentUserId, text: newComment };
            setComments([...comments, comment]);
            setNewComment('');
        }
    };

    return (
        <div className="bg-white rounded-lg shadow-md p-4 mb-4">
            <div className="flex items-start gap-3 mb-2">
                <Avatar className="h-9 w-9">
                    <AvatarImage src={user?.avatar} alt={user?.name} />
                    <AvatarFallback>{user?.name.substring(0, 2)}</AvatarFallback>
                </Avatar>
                <div>
                    <h3 className="text-lg font-semibold">{user?.name}</h3>
                    <p className="text-sm text-gray-500">
                        {new Date(post.timestamp).toLocaleDateString()}
                    </p>
                </div>
            </div>
            <p className="text-gray-700 mb-2 whitespace-pre-wrap break-words">{post.text}</p>
            <div className="flex items-center justify-between mb-2">
                <Button variant="ghost" size="sm" onClick={handleLike}>
                    {likes.includes(currentUserId) ? 'Unlike' : 'Like'} ({likes.length})
                </Button>
                <Button variant="ghost" size="sm" onClick={() => setShowComments(!showComments)}>
                    Comments ({comments.length})
                </Button>
            </div>
            <AnimatePresence>
            {showComments && (
                <motion.div
                initial={{ opacity: 0, height: 0 }}
                animate={{ opacity: 1, height: 'auto' }}
                exit={{ opacity: 0, height: 0 }}
                transition={{ duration: 0.2 }}
                className="mt-2 space-y-2"
                >
                {comments.map((comment, index) => {
                    const commentUser = getUserById(comment.userId);
                    return (
                    <div key={index} className="flex items-start gap-2">
                        <Avatar className="h-6 w-6">
                            <AvatarImage src={commentUser?.avatar} alt={commentUser?.name}/>
                            <AvatarFallback>{commentUser?.name.substring(0,2)}</AvatarFallback>
                        </Avatar>
                        <div>
                            <p className="text-sm font-medium">{commentUser?.name}</p>
                            <p className="text-sm text-gray-700">{comment.text}</p>
                        </div>
                    </div>
                )})}
                <div className="flex gap-2">
                    <Input
                        type="text"
                        placeholder="Add a comment..."
                        value={newComment}
                        onChange={(e) => setNewComment(e.target.value)}
                        className="flex-1"
                    />
                    <Button size="sm" onClick={handleAddComment} disabled={!newComment.trim()}>
                        Post
                    </Button>
                </div>
                </motion.div>
            )}
            </AnimatePresence>
        </div>
    );
};

// Notification Component
const NotificationItem = ({ notification }: { notification: typeof mockNotifications[0] }) => {
  const user = getUserById(notification.sourceUserId);

  let text = '';
  switch (notification.type) {
    case 'like':
      text = `${notification.sourceUserName} liked your post.`;
      break;
    case 'comment':
      text = `${notification.sourceUserName} commented on your post.`;
      break;
    default:
      text = 'New notification.';
  }

  return (
    <div className={cn(
        "flex items-center gap-3 p-2 rounded-md",
        notification.read ? "bg-gray-100" : "bg-white"
        )}>
      <Avatar className="h-8 w-8">
        <AvatarImage src={user?.avatar} alt={user?.name} />
        <AvatarFallback>{user?.name.substring(0, 2)}</AvatarFallback>
      </Avatar>
      <div>
        <p className="text-sm">{text}</p>
        <p className="text-xs text-gray-500">
          {new Date(notification.timestamp).toLocaleTimeString()}
        </p>
      </div>
    </div>
  );
};

// Main App Component
const SwiftChatApp = () => {
  const [currentUserId, setCurrentUserId] = useState('1'); // Replace with actual authentication
  const [currentRoomId, setCurrentRoomId] = useState<string | null>('room1');
  const [isVideoCallOpen, setIsVideoCallOpen] = useState(false);
  const [isCameraOn, setIsCameraOn] = useState(true);
  const [isMicOn, setIsMicOn] = useState(true);
  const [isDirectChatOpen, setIsDirectChatOpen] = useState(false);
  const [directChatUserId, setDirectChatUserId] = useState<string | null>(null);
  const [rooms, setRooms] = useState(mockRooms);
  const [showProfile, setShowProfile] = useState(false);
  const [showSettings, setShowSettings] = useState(false);
  const [notifications, setNotifications] = useState(mockNotifications);
  const [showNotifications, setShowNotifications] = useState(false);
  const [showFeed, setShowFeed] = useState(false);
  const [posts, setPosts] = useState(mockPosts);

  // Video Call Handlers
  const handleVideoCallStart = (roomId: string) => {
    setCurrentRoomId(roomId);
    setIsVideoCallOpen(true);
  };

  const handleVoiceCallStart = (roomId: string) => {
    setCurrentRoomId(roomId);
    setIsVideoCallOpen(true); // Reuse the VideoCall component, but you might want to differentiate in the future
    setIsCameraOn(false); // Optionally, turn off camera for voice calls
  };

  const handleVideoCallClose = () => {
    setIsVideoCallOpen(false);
    setCurrentRoomId(null);
  };

  const handleToggleCamera = () => {
    setIsCameraOn(prev => !prev);
  };

  const handleToggleMic = () => {
    setIsMicOn(prev => !prev);
  };

  const handleStartDirectChat = (userId: string) => {
    setDirectChatUserId(userId);
    setIsDirectChatOpen(true);
  };

  const handleCloseDirectChat = () => {
    setIsDirectChatOpen(false);
    setDirectChatUserId(null);
  };

  const handleCreateRoom = (roomName: string) => {
    const newRoom = {
      id: `room-${Date.now()}`,
      name: roomName,
      participants: [currentUserId], // The creator is automatically a participant
      type: 'public' // Default to public, add option later
    };
    setRooms(prevRooms => [...prevRooms, newRoom]);
    setCurrentRoomId(newRoom.id); // Optionally, automatically join the new room
  };

  const handleJoinRoom = (roomId: string) => {
    setCurrentRoomId(roomId);
  };

  const handleLeaveRoom = (roomId: string) => {
    setCurrentRoomId(null);
    // Optionally, remove user from room's participant list
    setRooms(prevRooms =>
      prevRooms.map(room =>
        room.id === roomId
          ? { ...room, participants: room.participants.filter(id => id !== currentUserId) }
          : room
      )
    );
  };

  const handleSendMessage = (roomId: string, message: string) => {
    // In a real app, you'd send this message to the server
    console.log(`Sending message "${message}" to room ${roomId} from user ${currentUserId}`);
    // Update local state (for immediate display)
    // setMessages(prevMessages => [...prevMessages, { id: `m${Date.now()}`, userId: currentUserId, text: message, timestamp: Date.now() }]);
  };

  const handleLogout = () => {
    // Implement actual logout logic (e.g., clear session, tokens)
    setCurrentUserId(null); // For demonstration, just reset the user ID
    // Redirect to login page or home page
  };

  const markNotificationAsRead = (notificationId: string) => {
    setNotifications(prev =>
      prev.map(n =>
        n.id === notificationId ? { ...n, read: true } : n
      )
    );
  };

  const markAllNotificationsAsRead = () => {
    setNotifications(prev => prev.map(n => ({ ...n, read: true })));
  };

  const handleCreatePost = (text: string) => {
      if(text.trim()){
          const newPost = {
            id: `post-${Date.now()}`,
            userId: currentUserId,
            text,
            timestamp: Date.now(),
            likes: [],
            comments: [],
          }
          setPosts(prev => [newPost, ...prev]);
          setShowFeed(true);
      }
  }

  if (!currentUserId) {
    // Render login/signup page here (replace with your actual authentication UI)
    return (
      <div className="flex items-center justify-center h-screen bg-gray-100">
        <div className="bg-white p-8 rounded-lg shadow-md w-full max-w-md">
          <h2 className="text-2xl font-bold mb-6 text-center">Welcome to SwiftChat</h2>
          <p className="text-gray-700 mb-4 text-center">
            Please log in to continue.
          </p>
          <Input type="email" placeholder="Email" className="mb-4" />
          <Input type="password" placeholder="Password" className="mb-6" />
          <Button className="w-full">Log In</Button>
          <p className="mt-4 text-center text-sm text-gray-600">
            Don't have an account? <a href="#" className="text-blue-500">Sign up</a>
          </p>
        </div>
      </div>
    );
  }

  return (
    <div className="flex h-screen bg-gray-100">
      <Sidebar
        rooms={rooms}
        onCreateRoom={handleCreateRoom}
        onJoinRoom={handleJoinRoom}
        onShowProfile={() => setShowProfile(true)}
        onShowSettings={() => setShowSettings(true)}
        onLogout={handleLogout}
        onShowFeed={() => setShowFeed(true)}
      />
      <div className="flex-1 flex overflow-hidden">
        <UserList users={mockUsers} onStartDirectChat={handleStartDirectChat} />
        {showFeed ? (
            <div className='flex-1 overflow-y-auto p-4'>
                <h2 className='text-2xl font-semibold mb-4'>Social Feed</h2>
                <CreatePostForm onCreatePost={handleCreatePost}/>
                {posts.map(post => (
                    <Post key={post.id} post={post} currentUserId={currentUserId}/>
                ))}
            </div>
        ) : currentRoomId ? (
          <ChatRoom
            roomId={currentRoomId}
            currentUserId={currentUserId}
            onVideoCallStart={handleVideoCallStart}
            onVoiceCallStart={handleVoiceCallStart}
            onMessageSend={handleSendMessage}
            onRoomLeave={handleLeaveRoom}
          />
        ) : (
          <div className="flex-1 flex items-center justify-center">
            <p className="text-gray-500">Select a room to join or create a new one.</p>
          </div>
        )}
      </div>

      {/* Video Call Modal */}
      {isVideoCallOpen && currentRoomId && (
        <VideoCall
          roomId={currentRoomId}
          onClose={handleVideoCallClose}
          isInitiator={true}
          onToggleCamera={handleToggleCamera}
          onToggleMic={handleToggleMic}
          isCameraOn={isCameraOn}
          isMicOn={isMicOn}
        />
      )}

      {/* Direct Chat Modal (Sheet) */}
      <Sheet open={isDirectChatOpen} onOpenChange={setIsDirectChatOpen}>
        <SheetContent side="right" className="w-full sm:max-w-md">
          <SheetHeader>
            <SheetTitle>Direct Chat with {directChatUserId ? getUserById(directChatUserId)?.name : 'User'}</SheetTitle>
            <SheetDescription>
              This is a private chat between you and another user.
            </SheetDescription>
          </SheetHeader>
          {directChatUserId && (
            <ChatRoom
              roomId={`direct-${currentUserId}-${directChatUserId}`} // Unique room ID for direct chat
              currentUserId={currentUserId}
              onVideoCallStart={() => { }} // Disable video/voice calls in direct chat for now
              onVoiceCallStart={() => { }}
              onMessageSend={handleSendMessage}
              onRoomLeave={() => { }} // Direct chats aren't "left" in the same way as rooms
            />
          )}
          <SheetClose asChild>
            <Button variant="secondary" className="mt-4">Close</Button>
          </SheetClose>
        </SheetContent>
      </Sheet>

      {/* Profile Sheet */}
      <Sheet open={showProfile} onOpenChange={setShowProfile}>
        <SheetContent>
          <SheetHeader>
            <SheetTitle>Profile</SheetTitle>
            <SheetDescription>
              View and edit your profile information.
            </SheetDescription>
          </SheetHeader>
          <div className="space-y-4 py-4">
            <div className="flex items-center gap-4">
              <Avatar className="h-12 w-12">
                <AvatarImage src="https://source.unsplash.com/random/100x100/?portrait" alt="Profile" />
                <AvatarFallback>JD</AvatarFallback>
              </Avatar>
              <div>
                <h3 className="text-lg font-semibold">John Doe</h3>
                <p className="text-sm text-gray-500">john.doe@example.com</p>
              </div>
            </div>

            <div>
              <label htmlFor="name" className="block text-sm font-medium text-gray-700">Name</label>
              <Input id="name" defaultValue="John Doe" className="mt-1" />
            </div>
            <div>
              <label htmlFor="email" className="block text-sm font-medium text-gray-700">Email</label>
              <Input id="email" defaultValue="john.doe@example.com" className="mt-1" />
            </div>
            <div>
              <label htmlFor="bio" className="block text-sm font-medium text-gray-700">Bio</label>
              <Textarea id="bio" defaultValue="Software Engineer" className="mt-1" />
            </div>
          </div>
          <SheetClose asChild>
            <Button>Save Changes</Button>
          </SheetClose>
        </SheetContent>
      </Sheet>

      {/* Settings Sheet */}
      <Sheet open={showSettings} onOpenChange={setShowSettings}>
        <SheetContent>
          <SheetHeader>
            <SheetTitle>Settings</SheetTitle>
            <SheetDescription>
              Configure your app preferences.
            </SheetDescription>
          </SheetHeader>
          <div className="space-y-4 py-4">
            <div>
              <h3 className="text-lg font-semibold mb-2">General</h3>
              <div className="space-y-2">
                <div className="flex items-center justify-between">
                  <label htmlFor="notifications" className="text-sm font-medium text-gray-700">Enable Notifications</label>
                  <Button variant="outline">On</Button>
                </div>
                <div className="flex items-center justify-between">
                  <label htmlFor="darkMode" className="text-sm font-medium text-gray-700">Dark Mode</label>
                  <Button variant="outline">Off</Button>
                </div>
              </div>
            </div>
            <div>
              <h3 className="text-lg font-semibold mb-2">Account</h3>
              <div className="space-y-2">
                <div className="flex items-center justify-between">
                  <label htmlFor="changePassword" className="text-sm font-medium text-gray-700">Change Password</label>
                  <Button variant="outline">Change</Button>
                </div>
              </div>
            </div>
          </div>
          <SheetClose asChild>
            <Button>Save Settings</Button>
          </SheetClose>
        </SheetContent>
      </Sheet>

      {/* Notification Sheet */}
      <Sheet open={showNotifications} onOpenChange={setShowNotifications}>
        <SheetContent side="right" className="w-full sm:max-w-md">
          <SheetHeader>
            <SheetTitle>Notifications</SheetTitle>
            <SheetDescription>
              You have {notifications.filter(n => !n.read).length} new notifications.
            </SheetDescription>
            <Button variant="outline" size="sm" onClick={markAllNotificationsAsRead}>
              Mark all as read
            </Button>
          </SheetHeader>
          <div className="overflow-y-auto py-4 space-y-2">
            {notifications.map(notification => (
              <NotificationItem key={notification.id} notification={notification} />
            ))}
          </div>
          <SheetClose asChild>
            <Button variant="secondary" className="mt-4">Close</Button>
          </SheetClose>
        </SheetContent>
      </Sheet>
    </div>
  );
};

const CreatePostForm = ({onCreatePost}: {onCreatePost: (text: string) => void}) => {
    const [text, setText] = useState('');

    const handlePost = () => {
        onCreatePost(text);
        setText('');
    }

    return (
        <div className='bg-white rounded-lg shadow-md p-4 mb-4'>
            <Textarea
                placeholder="What's on your mind?"
                value={text}
                onChange={(e) => setText(e.target.value)}
                className="resize-none mb-2"
                rows={3}
            />
            <Button onClick={handlePost} disabled={!text.trim()} className="w-full">
                Post
            </Button>
        </div>
    )
}

export default SwiftChatApp;

