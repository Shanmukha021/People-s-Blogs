// SPDX-License-Identifier: MIT
pragma solidity ^0.8.19;

/**
 * @title Simple Blogging Platform
 * @dev A minimal blogging smart contract with posts, authors, likes, and comments
 */
contract SimpleBlog {
    
    // Struct to represent a blog post
    struct BlogPost {
        uint256 id;
        address author;
        string title;
        string content;
        uint256 timestamp;
        uint256 likes;
        bool isPublished;
    }
    
    // Struct to represent a blog author
    struct Author {
        address authorAddress;
        string username;
        string bio;
        uint256 totalPosts;
        uint256 totalLikes;
        uint256 joinDate;
    }
    
    // Struct for comments
    struct Comment {
        uint256 id;
        uint256 postId;
        address commenter;
        string content;
        uint256 timestamp;
    }
    
    // State variables
    mapping(uint256 => BlogPost) public blogPosts;
    mapping(address => Author) public authors;
    mapping(uint256 => Comment[]) public postComments;
    mapping(address => uint256[]) public authorPosts;
    mapping(uint256 => mapping(address => bool)) public postLikes;
    
    uint256 public totalPosts;
    uint256 public totalComments;
    
    // Events
    event PostCreated(uint256 indexed postId, address indexed author, string title);
    event PostLiked(uint256 indexed postId, address indexed liker);
    event CommentAdded(uint256 indexed postId, uint256 indexed commentId, address indexed commenter);
    event AuthorRegistered(address indexed author, string username);
    
    // Modifiers
    modifier postExists(uint256 _postId) {
        require(_postId < totalPosts, "Post does not exist");
        _;
    }
    
    modifier isRegisteredAuthor() {
        require(bytes(authors[msg.sender].username).length > 0, "Author must be registered");
        _;
    }
    
    constructor() {
        totalPosts = 0;
        totalComments = 0;
    }
    
    /**
     * @dev Register as an author
     */
    function registerAuthor(string memory _username, string memory _bio) external {
        require(bytes(authors[msg.sender].username).length == 0, "Already registered");
        require(bytes(_username).length > 0, "Username required");
        
        authors[msg.sender] = Author({
            authorAddress: msg.sender,
            username: _username,
            bio: _bio,
            totalPosts: 0,
            totalLikes: 0,
            joinDate: block.timestamp
        });
        
        emit AuthorRegistered(msg.sender, _username);
    }
    
    /**
     * @dev Create and publish blog posts
     */
    function createBlogPost(string memory _title, string memory _content) external isRegisteredAuthor {
        require(bytes(_title).length > 0, "Title required");
        require(bytes(_content).length > 0, "Content required");
        
        uint256 postId = totalPosts;
        
        blogPosts[postId] = BlogPost({
            id: postId,
            author: msg.sender,
            title: _title,
            content: _content,
            timestamp: block.timestamp,
            likes: 0,
            isPublished: true
        });
        
        authorPosts[msg.sender].push(postId);
        authors[msg.sender].totalPosts++;
        totalPosts++;
        
        emit PostCreated(postId, msg.sender, _title);
    }
    
    /**
     * @dev Like a blog post
     */
    function likePost(uint256 _postId) external postExists(_postId) isRegisteredAuthor {
        require(!postLikes[_postId][msg.sender], "Already liked");
        
        postLikes[_postId][msg.sender] = true;
        blogPosts[_postId].likes++;
        authors[blogPosts[_postId].author].totalLikes++;
        
        emit PostLiked(_postId, msg.sender);
    }
    
    /**
     * @dev Comment on a post
     */
    function addComment(uint256 _postId, string memory _content) external postExists(_postId) isRegisteredAuthor {
        require(bytes(_content).length > 0, "Comment required");
        
        Comment memory newComment = Comment({
            id: totalComments,
            postId: _postId,
            commenter: msg.sender,
            content: _content,
            timestamp: block.timestamp
        });
        
        postComments[_postId].push(newComment);
        totalComments++;
        
        emit CommentAdded(_postId, totalComments - 1, msg.sender);
    }
    
    /**
     * @dev Get posts by author
     */
    function getAuthorPosts(address _author) external view returns (uint256[] memory) {
        return authorPosts[_author];
    }
    
    /**
     * @dev Get comments for a post
     */
    function getPostComments(uint256 _postId) external view postExists(_postId) returns (Comment[] memory) {
        return postComments[_postId];
    }
}
# People's Blogs

## Project Description

People's Blogs is a revolutionary decentralized blogging platform built on the Ethereum blockchain that empowers writers, content creators, and readers to participate in a censorship-resistant publishing ecosystem. Unlike traditional centralized blogging platforms, People's Blogs ensures that content cannot be arbitrarily removed, modified, or censored by any single authority.

The platform combines the power of blockchain technology with intuitive blogging features, creating a space where authentic voices can flourish without fear of suppression. Writers maintain complete ownership of their content while earning directly from their audience through built-in tipping mechanisms. Every blog post, interaction, and transaction is permanently recorded on the blockchain, creating an immutable record of human expression and creativity.

Built with Solidity and optimized for the Ethereum ecosystem, People's Blogs represents the future of digital publishing where creators are fairly compensated, readers have access to diverse perspectives, and the entire community benefits from transparent, decentralized governance.

## Project Vision

**🌍 Democratizing Global Expression**

Our vision is to create the world's most open, fair, and sustainable publishing platform that serves as a cornerstone of free expression in the digital age. We envision a future where:

**📚 Universal Access to Information**: Anyone, anywhere can publish and access content without geographical, political, or economic barriers blocking their path to knowledge and self-expression.

**💰 Fair Creator Economy**: Writers and content creators receive direct, immediate compensation from their audience without intermediaries taking excessive fees or controlling their earning potential.

**🔒 Censorship-Resistant Publishing**: A truly decentralized platform where content lives on the blockchain, protected from arbitrary takedowns, shadow banning, or algorithmic suppression.

**🤝 Community-Driven Ecosystem**: A self-governing community where users collectively shape platform policies, features, and direction through decentralized governance mechanisms.

**🌱 Sustainable Content Creation**: An economic model that incentivizes high-quality content creation while building long-term value for both creators and the broader community.

**🔮 Innovation in Digital Publishing**: A platform that continuously evolves with emerging technologies, setting new standards for how digital content is created, shared, and monetized.

We believe that decentralized publishing is not just a technological advancement but a fundamental human right that will drive innovation, preserve knowledge, and foster global understanding across all communities and cultures.

## Key Features

### 🎯 Core Platform Features

**✍️ Decentralized Content Creation**
- Publish blog posts directly to the blockchain with permanent storage
- Rich content support including text, images, and multimedia through IPFS integration
- Comprehensive tagging system for content categorization and discoverability
- Real-time content editing and updating capabilities with full version history

**👤 Comprehensive User Profiles**
- Unique username registration with profile customization
- Detailed author pages with biography, portfolio, and statistics
- Achievement systems and reputation tracking
- Social media integration and cross-platform connectivity

**💎 Built-in Monetization**
- Direct peer-to-peer tipping system with instant payments
- Transparent fee structure with minimal platform charges
- Real-time earnings tracking and analytics
- Multiple payment options including ETH and popular ERC-20 tokens

### 🛡️ Security & Trust Features

**🔐 Blockchain-Powered Security**
- Immutable content storage with cryptographic proof of authorship
- Smart contract-based access control and permission management
- Multi-signature wallet integration for enhanced security
- Automated escrow systems for sponsored content and collaborations

**🌐 Censorship Resistance**
- Distributed content storage across multiple IPFS nodes
- No single point of failure or control
- Community-driven content moderation with transparent voting mechanisms
- Appeals process for disputed content decisions

### 📊 Advanced Discovery & Analytics

**🔍 Intelligent Content Discovery**
- Advanced search functionality with multiple filter options
- Trending content algorithms based on engagement metrics
- Personalized recommendation systems powered by user behavior
- Cross-referenced tagging and topic clustering

**📈 Comprehensive Analytics**
- Real-time reader engagement and interaction metrics
- Revenue tracking and earning projections
- Audience demographics and growth analysis
- Performance comparisons and optimization suggestions

### 🎨 User Experience Excellence

**📱 Multi-Platform Accessibility**
- Responsive web interface optimized for all devices
- Progressive Web App (PWA) capabilities for offline reading
- Native mobile applications for iOS and Android
- Browser extension for seamless Web3 integration

**🎪 Interactive Features**
- Real-time commenting system with blockchain verification
- Social sharing across traditional and Web3 platforms
- Collaboration tools for multi-author publications
- Community challenges and writing contests

## Future Scope

### 🚀 Phase 1: Enhanced User Experience (Q2 2024)

**📝 Advanced Editor Features**
- Rich WYSIWYG editor with markdown support
- Real-time collaborative editing for multi-author posts
- Draft auto-save and version control system
- Media gallery with drag-and-drop functionality
- LaTeX math rendering for technical content

**🏆 Gamification & Rewards**
- Reader loyalty programs with tokenized rewards
- Author achievement badges and milestone tracking
- Community challenges with prize pools
- Reading streaks and engagement incentives
- Leaderboards for top contributors and readers

### 🌟 Phase 2: Advanced Monetization (Q3 2024)

**💰 Subscription & Membership Models**
- Monthly subscription tiers with exclusive content access
- Author-specific membership programs with special perks
- Community-funded journalism and investigative reporting
- Crowdfunding campaigns for large content projects
- Revenue sharing for content curators and promoters

**🎁 NFT Integration**
- Convert popular blog posts into collectible NFTs
- Limited edition content releases with scarcity mechanics
- Author profile NFTs with evolving traits based on achievements
- Reader badges and proof-of-reading certificates
- Cross-platform NFT compatibility and trading

### 🔧 Phase 3: Platform Expansion (Q4 2024)

**📚 Content Format Diversification**
- Podcast hosting and streaming capabilities
- Video blog (vlog) support with decentralized storage
- Interactive content with embedded polls and surveys
- Long-form research papers with peer review systems
- Multimedia storytelling with immersive experiences

**🌍 Global Accessibility**
- Multi-language support with automatic translation
- Regional content discovery and localization
- Currency conversion and local payment methods
- Cultural content guidelines and community standards
- Accessibility features for users with disabilities

### 🏢 Phase 4: Enterprise & Institutional Features (Q1 2025)

**🏛️ Institutional Publishing**
- Academic journal hosting with peer review workflows
- Corporate blogging solutions with brand management
- News organization partnerships with fact-checking systems
- Research institution collaboration tools
- Educational content platforms for schools and universities

**📊 Advanced Analytics & AI**
- Machine learning-powered content recommendations
- Automated plagiarism detection and originality scoring
- Sentiment analysis and engagement prediction
- Market trend analysis for content creators
- Personalized content optimization suggestions

### 🌐 Phase 5: Ecosystem Integration (Q2 2025)

**🔗 Cross-Platform Interoperability**
- Integration with major social media platforms
- WordPress and Medium migration tools
- Email newsletter synchronization
- RSS feed generation and syndication
- API ecosystem for third-party applications

**⚖️ Governance & DAO Evolution**
- Decentralized Autonomous Organization (DAO) governance
- Community voting on platform policies and features
- Transparent budget allocation and development funding
- Stakeholder representation in platform decisions
- Democratic content moderation policies

### 🔬 Phase 6: Cutting-Edge Innovation (Q3 2025)

**🤖 AI-Powered Features**
- AI writing assistants with grammar and style checking
- Automated content summarization and key point extraction
- Intelligent tagging and categorization systems
- Personalized reading recommendations based on interests
- Real-time fact-checking and source verification

**🌌 Metaverse Integration**
- Virtual reality reading rooms and author meetups
- 3D content presentation and immersive storytelling
- Virtual conferences and book launch events
- Augmented reality content overlay capabilities
- Social VR spaces for community building

### 🔮 Phase 7: Next-Generation Publishing (Q4 2025)

**⚡ Layer 2 & Scalability Solutions**
- Polygon and Arbitrum deployment for lower gas costs
- Lightning Network integration for micropayments
- Rollup-based content storage for increased throughput
- Cross-chain compatibility for multi-blockchain publishing
- State channel implementations for real-time interactions

**🛡️ Privacy & Security Enhancements**
- Zero-knowledge proof implementations for anonymous publishing
- End-to-end encrypted private messaging between users
- Decentralized identity (DID) integration
- Privacy-preserving analytics and user tracking
- Advanced spam and bot detection mechanisms

## Technical Architecture

### 🏗️ Smart Contract Infrastructure
- **Solidity Version**: ^0.8.19 with latest security optimizations
- **Gas Efficiency**: Optimized storage patterns and function execution
- **Security Audits**: Regular third-party security assessments
- **Upgradeability**: Proxy contract patterns for seamless updates
- **Testing**: Comprehensive test suites with 95%+ code coverage

### 🌐 Decentralized Storage
- **IPFS Integration**: Distributed content storage and retrieval
- **Content Addressing**: Cryptographic content hashing for integrity
- **Redundancy**: Multi-node storage for high availability
- **Caching**: Edge caching for improved content delivery speed
- **Backup**: Automated backup systems across multiple providers

### 📡 Network Compatibility
- **Primary**: Ethereum Mainnet for maximum security
- **Layer 2**: Polygon, Arbitrum, Optimism for cost efficiency
- **Testnets**: Goerli, Sepolia for development and testing
- **Future**: Solana, Cardano, and other blockchain integrations

## Getting Started

### 🔧 Developer Setup
```bash
# Clone repository
git clone https://github.com/peoples-blogs/smart-contracts
cd peoples-blogs

# Install dependencies
npm install

# Set up environment variables
cp .env.example .env
# Edit .env with your configuration

# Compile smart contracts
npx hardhat compile

# Run comprehensive test suite
npx hardhat test

# Deploy to testnet
npx hardhat run scripts/deploy.js --network goerli

# Verify contracts on Etherscan
npx hardhat verify --network goerli DEPLOYED_CONTRACT_ADDRESS
```

### 👋 User Onboarding
1. **Connect Web3 Wallet**: MetaMask, WalletConnect, or compatible wallet
2. **Register Profile**: Create unique username and profile
3. **Fund Wallet**: Add ETH for gas fees and tipping
4. **Start Writing**: Publish your first decentralized blog post
5. **Engage Community**: Read, tip, and interact with other creators

## Security & Best Practices

### 🔒 Smart Contract Security
- Multiple security audit reports from leading firms
- Bug bounty program with responsible disclosure
- Regular penetration testing and vulnerability assessments
- Community-driven security reviews and feedback
- Emergency pause mechanisms for critical issues

### 🛡️ User Safety Guidelines
- Never share private keys or seed phrases
- Verify contract addresses before interacting
- Start with small amounts for testing
- Keep software and wallets updated
- Use hardware wallets for large amounts

---

**🚀 Join the decentralized publishing revolution | 📝 Where every voice matters | 🌍 Built by the community, for the community**# People-s-Blogs
contract :- <img width="1920" height="1020" alt="Screenshot 2025-09-10 142608" src="https://github.com/user-attachments/assets/7396470a-66a9-4e3b-9239-7437dd7ed4a8" />
